# Tallinn GIS - Laur Koolide Kaugused API Documentation

## Overview
See repository majutab **avalikku API dokumentatsiooni** koolide kauguste andmetele juurdepääsuks ArcGIS FeatureServeri kaudu. Teenuse eesmärk on  pakkuda geoinfo andmeid, et X ja Y koordinaatide alusel saaks leida x lähimat kooli mingi aadressi jaoks 

**Base URL:**
```
Teenus: https://gis.tallinn.ee/arcgis/rest/services/Hosted/laur_koolide_kaugused/FeatureServer
Andmete päring: https://gis.tallinn.ee/arcgis/rest/services/Hosted/laur_koolide_kaugused/FeatureServer/0/query?
```

## How to Use
Open the interactive Swagger UI:
```
https://tallinngis.github.io/LAUR/swagger_ui.html
```

## Authentication
- Authorization: Bearer {API_KEY}? OR X-Esri-Authorization: Bearer <token>

## Endpoints

### Service-level
- `GET /` – Service metadata  
  *Example:* `/FeatureServer?f=json`

- `GET /layers` – List all layers in the service  
  *Example:* `/FeatureServer/layers?f=json`

### Layer-level
- `GET /{layerId}` – Layer details  
  *Example:* `/FeatureServer/{layerId}?f=json`

- `GET /{layerId}/query` – Query features (supports GET and POST)  
  *Example:* `/FeatureServer/{layerId}/query`

### Common ArcGIS Query Parameters
- **`where`** – SQL expression to filter features  
  *Example:* `where=1=1` or `where=oppekeel='eestikeelne'`

- **`outFields`** – Comma-separated list of fields to return  
  *Example:* `outFields=*` or `outFields=nimi,ehisid`

- **`geometry`** – Spatial filter geometry (point, envelope, polygon)  
  *Example:* `geometry=6590137.73,541389.7`

- **`geometryType`** – Type of geometry provided  
  *Example:* `geometryType=esriGeometryPoint`

- **`inSR`** – Spatial reference of input geometry  
  *Example:* `inSR=3301`

- **`outSR`** – Spatial reference for output geometry  
  *Example:* `outSR=3301`

- **`spatialRel`** – Spatial relationship for filtering  
  *Example:* `spatialRel=esriSpatialRelIntersects`

- **`distance`** – Buffer distance for point queries  
  *Example:* `distance=100`

- **`units`** – Units for buffer distance  
  *Example:* `units=esriSRUnit_Meter`

- **`orderByFields`** – Sort results by one or more fields  
  *Example:* `orderByFields=rank ASC`

- **`returnGeometry`** – Whether to include geometry in the response  
  *Example:* `returnGeometry=true`

- **`resultRecordCount`** – Maximum number of records to return  
  *Example:* `resultRecordCount=10`

- **`resultOffset`** – Offset for pagination  
  *Example:* `resultOffset=0`

- **`returnDistinctValues`** – Return distinct values only  
  *Example:* `returnDistinctValues=true`

- **`groupByFieldsForStatistics`** – Fields to group by for statistics  
  *Example:* `groupByFieldsForStatistics=oppekeel`

- **`outStatistics`** – JSON defining statistical operations  
  *Example:*  
  ```json
  outStatistics=[{"statisticType":"avg","onStatisticField":"Distance","outStatisticFieldName":"avg_distance"}]
  ``
- **`f`** – Output format  
  *Example:* `f=json`

- **`token`** – ArcGIS authentication token (if required)  
  *Example:* `token=YOUR_TOKEN`


## Request Format

- **Protocol:** HTTPS  
- **Response format:** JSON (ArcGIS REST API responses are typically JSON; GeoJSON may be used if supported)  
- **Encoding:** UTF-8  
- **Methods:** GET and POST (POST recommended for complex queries or large payloads)  
- **Supported POST Content-Types:**  
  - `application/x-www-form-urlencoded` (recommended for query operations)  
  - `multipart/form-data` (used for operations involving file uploads, e.g., attachments) 

## Quick Start Examples

### GET Example
```http
GET https://gis.tallinn.ee/arcgis/rest/services/Hosted/laur_koolide_kaugused/FeatureServer/0/query
  ?where=1=1 #all objects
  &outFields=* #all fields
  &f=json
  &resultRecordCount=10
```

### Example: Spatial Query with Buffer

This example returns all features within a 10-meter radius of a given point.

**Parameters:**
- `geometry=<X>,<Y>` – Input point coordinates
- `geometryType=esriGeometryPoint` – Geometry type
- `inSR=3301` – Spatial reference of input geometry
- `distance=<tolerance>` – Buffer distance (e.g., 10 meters)
- `units=esriSRUnit_Meter` – Units for buffer
- `returnGeometry=false` – Do not return geometry in response
- `outFields=*` – Return all fields
- `f=json` – Output format

**Example Request:**
```http
GET https://gis.tallinn.ee/arcgis/rest/services/Hosted/laur_koolide_kaugused/FeatureServer/0/query
  ?geometry=6579566.77,535528.24
  &geometryType=esriGeometryPoint
  &inSR=3301
  &distance=10
  &units=esriSRUnit_Meter
  &outFields=*
  &returnGeometry=false
  &f=json
```
### How to return only the nearest point

ArcGIS FeatureServer does not compute distance to your input point.  
To get the nearest object:

1. Query all features within a buffer around your location:
   - `distance=<meters>`
   - `returnGeometry=true`
2. Compute distance client‑side (e.g., Euclidean distance in EPSG:3301).
3. Select the feature with the smallest distance.

Example request:
```http
GET https://gis.tallinn.ee/arcgis/rest/services/Hosted/laur_koolide_kaugused/FeatureServer/0/query
  ?geometry=6579566.77,535528.24
  &geometryType=esriGeometryPoint
  &inSR=3301
  &distance=10
  &units=esriSRUnit_Meter
  &outFields=*
  &returnGeometry=true
  &f=json

---

## ✔ Why server-side sorting cannot return the nearest point

ArcGIS FeatureServer **cannot sort by geometric distance** to your input point.  
`orderByFields=distance ASC` works **only** if your layer has a real attribute field named `distance`.  
Since your service does not contain such a field, sorting by distance must be done **client‑side**.

This is why:

- `orderByFields=distance ASC` → **does not work**  
- `resultRecordCount=1` → **does not guarantee the nearest**  
- `returnGeometry=false` → makes it impossible to compute the nearest  

Therefore, always use `returnGeometry=true` when calculating nearest features.

---

## ✔ Example: Client-side nearest point calculation (JavaScript)

```js
// Input point in EPSG:3301 (meters)
const input = { x: 6579566.77, y: 535528.24 };

function distance(a, b) {
  return Math.hypot(a.x - b.x, a.y - b.y); // Euclidean distance
}

async function findNearest() {
  const url = "https://gis.tallinn.ee/arcgis/rest/services/Hosted/laur_koolide_kaugused/FeatureServer/0/query";
  const params = new URLSearchParams({
    geometry: `${input.x},${input.y}`,
    geometryType: "esriGeometryPoint",
    inSR: "3301",
    distance: "100",
    units: "esriSRUnit_Meter",
    outFields: "*",
    returnGeometry: "true",
    f: "json"
  });

  const res = await fetch(`${url}?${params}`);
  const data = await res.json();

  let nearest = null;
  let bestDist = Infinity;

  for (const f of data.features) {
    const g = f.geometry;
    if (!g) continue;

    const d = distance(input, g);
    if (d < bestDist) {
      bestDist = d;
      nearest = { distance: d, attributes: f.attributes, geometry: g };
    }
  }

  return nearest;
}

## ✔ Example: Client-side nearest point calculation (Python)

import math
import requests

input_x, input_y = 6579566.77, 535528.24

def dist(x1, y1, x2, y2):
    return math.hypot(x1 - x2, y1 - y2)

params = {
    "geometry": f"{input_x},{input_y}",
    "geometryType": "esriGeometryPoint",
    "inSR": "3301",
    "distance": "100",
    "units": "esriSRUnit_Meter",
    "outFields": "*",
    "returnGeometry": "true",
    "f": "json"
}

url = "https://gis.tallinn.ee/arcgis/rest/services/Hosted/laur_koolide_kaugused/FeatureServer/0/query"

response = requests.get(url, params=params).json()
features = response.get("features", [])

nearest = None
best_dist = float("inf")

for f in features:
    geom = f.get("geometry")
    if not geom:
        continue

    d = dist(input_x, input_y, geom["x"], geom["y"])
    if d < best_dist:
        best_dist = d
        nearest = {
            "distance": d,
            "attributes": f["attributes"],
            "geometry": geom
        }

print(nearest)

### POST Example (application/x-www-form-urlencoded)
```bash
curl -X POST   'https://gis.tallinn.ee/arcgis/rest/services/Hosted/laur_koolide_kaugused/FeatureServer/0/query'   -H 'Content-Type: application/x-www-form-urlencoded'   --data-urlencode 'where=1=1'   --data-urlencode 'outFields=*'   --data-urlencode 'returnGeometry=true'   --data-urlencode 'f=json'   --data-urlencode 'resultRecordCount=10'   --data-urlencode 'token=YOUR_TOKEN'
```

### POST Example (application/json)
```bash
curl -X POST   'https://gis.tallinn.ee/arcgis/rest/services/Hosted/laur_koolide_kaugused/FeatureServer/0/query'   -H 'Content-Type: application/json'   -d '{
    "where": "1=1",
    "outFields": "*",
    "returnGeometry": true,
    "resultRecordCount": 10,
    "f": "json",
    "token": "YOUR_TOKEN"
  }'
```
## Error Codes
- **200 OK** – Request successful
- **400 Bad Request** – Invalid query parameters
- **401 Unauthorized** – Missing or invalid token
- **403 Forbidden** – Insufficient privileges
- **500 Internal Server Error** – Server-side error

ArcGIS error response example:
```json
{
  "error": {
    "code": 400,
    "message": "Invalid query parameters",
    "details": ["Invalid geometry"]
  }
}

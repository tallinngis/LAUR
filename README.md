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
  *Example:* `orderByFields=Distance ASC`

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
To return only the nearest point:
```
&orderByFields=distance ASC
&resultRecordCount=1

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

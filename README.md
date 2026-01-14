# Tallinn GIS - Laur Koolide Kaugused API Documentation

## Overview
See repository majutab **avalikku API dokumentatsiooni** koolide kauguste andmetele juurdepääsuks ArcGIS FeatureServeri kaudu. API eesmärk on  pakkuda geoinfo andmeid, et X ja Y koordinaatide alusel saaks leida x lähimat kooli mingi aadressi jaoks 

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
Some endpoints may require an ArcGIS token:
- Obtain a token from your ArcGIS account.
- Authorization: Bearer {API_KEY}? or X-Esri-Authorization: Bearer <token>
- Pass it as a query parameter:
```
?token=YOUR_TOKEN
```

## Endpoints

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

- `POST /{layerId}/applyEdits` – Add, update, or delete features  
  *Example:* `/FeatureServer/{layerId}/applyEdits`

- `POST /{layerId}/generateRenderer` – Generate a renderer based on data  
  *Example:* `/FeatureServer/{layerId}/generateRenderer`

- `GET /{layerId}/queryDomains` – Query domain information for fields  
  *Example:* `/FeatureServer/{layerId}/queryDomains`

### Common ArcGIS Query Parameters
- **`where`** – SQL expression to filter features  
  *Example:* `where=1=1` or `where=oppekeel='eestikeelne'`

- **`outFields`** – Comma-separated list of fields to return  
  *Example:* `outFields=*` or `outFields=Name,Distance`

- **`geometry`** – Spatial filter geometry (point, envelope, polygon)  
  *Example:* `geometry=6590137.73,541389.7`

- **`geometryType`** – Type of geometry provided  
  *Example:* `geometryType=esriGeometryPoint`

- **`inSR`** – Spatial reference of input geometry  
  *Example:* `inSR=3301`

- **`outSR`** – Spatial reference for output geometry  
  *Example:* `outSR=3857`

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

## Request Format

- **Protocol:** HTTPS  
- **Data format:** JSON (ArcGIS REST API responses are typically JSON; GeoJSON may be used if supported)  
- **Encoding:** UTF-8  
- **Methods:** GET and POST (POST recommended for complex queries or large payloads)  

## Example Query
```
GET /0/query?where=1=1&outFields=*&f=json&resultRecordCount=10
```

## Error codes

## License
MIT License

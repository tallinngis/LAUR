# Tallinn GIS - Laur Koolide Kaugused API Documentation

## Overview
This repository hosts the **public API documentation** for accessing school distance data via the ArcGIS FeatureServer.

**Base URL:**
```
https://gis.tallinn.ee/arcgis/rest/services/Hosted/laur_koolide_kaugused/FeatureServer
```

## How to Use
Open the interactive Swagger UI:
```
https://tallinngis.github.io/LAUR/swagger_ui.html
```

## Authentication
Some endpoints may require an ArcGIS token:
- Obtain a token from your ArcGIS account.
- Pass it as a query parameter:
```
?token=YOUR_TOKEN
```

## Endpoints
- `GET /` – Service metadata
- `GET /{layerId}` – Layer details
- `GET /{layerId}/query` – Query features
- `POST /{layerId}/applyEdits` – Apply edits

### Common Query Parameters
- `where` – SQL filter (e.g., `1=1`)
- `outFields` – Fields to return (e.g., `*`)
- `geometry` – Spatial filter
- `spatialRel` – Spatial relationship (e.g., `esriSpatialRelIntersects`)
- `orderByFields` – Sort results
- `returnGeometry` – Include geometry in response
- `resultRecordCount` – Limit number of records

## Example Query
```
GET /0/query?where=1=1&outFields=*&f=json&resultRecordCount=10
```

## Contributing
Feel free to submit issues or pull requests to improve the documentation.

## License
MIT License


<p align="center">
  <img width="700" src="https://user-images.githubusercontent.com/10407788/236044449-f7d21e6c-c196-4f9e-b4b6-59c945e92644.png"/>
  <p align="center">Create a full Earth Observation API with Metadata, Raster and Vector services.</p>
</p>

<p align="center">
  <a href="https://github.com/developmentseed/eoAPI/actions?query=workflow%3ACI" target="_blank">
      <img src="https://github.com/developmentseed/eoAPI/workflows/CI/badge.svg" alt="Test">
  </a>
  <a href="https://github.com/developmentseed/eoAPI/blob/main/LICENSE" target="_blank">
      <img src="https://img.shields.io/github/license/developmentseed/titiler.svg" alt="Downloads">
  </a>
</p>

---

**Documentation**: <a href="https://developmentseed.org/eoAPI/" target="_blank">https://developmentseed.org/eoAPI/</a>

**Source Code**: <a href="https://github.com/developmentseed/eoAPI" target="_blank">https://github.com/developmentseed/eoAPI</a>

---

## **E**arth **O**bservation **API**

The objective of `eoAPI` is to combine *state of the art* project to create a full Earth Observation API for Metadata search (STAC), Raster and Feature/Vector services:

- **pgSTAC** database [https://github.com/stac-utils/pgstac](https://github.com/stac-utils/pgstac)

- **STAC API** built on top of [https://github.com/stac-utils/stac-fastapi](https://github.com/stac-utils/stac-fastapi)

- **STAC Items And Mosaic Raster Tiles** API built on top of [https://github.com/stac-utils/titiler-pgstac](https://github.com/stac-utils/titiler-pgstac)

- **OGC Features and Vector Tiles** API built on top of [https://github.com/developmentseed/tipg](https://github.com/developmentseed/tipg)


Note: Each service can be used/deployed independently but **eoAPI** also adds interconnection between them.

---

## Services

### STAC Metadata

A custom version of [stac-fastapi.pgstac](https://github.com/stac-utils/stac-fastapi) application, adding a **`TiTilerExtension`** and a simple **`Search Viewer`**.

- Full **stac-fastapi** implementation

- Simple STAC Search **viewer**

- **Proxy** to the Tiler endpoint for STAC Items

  When `TITILER_ENDPOINT` environement is set (pointing the `raster` application), additional endpoints will be added to the stac-fastapi application (see: [stac/extension.py](https://github.com/developmentseed/eoAPI/blob/main/src/eoapi/stac/eoapi/stac/extension.py)):

  - `/collections/{collectionId}/items/{itemId}/tilejson.json`: Return the `raster` tilejson for an item
  - `/collections/{collectionId}/items/{itemId}/viewer`: Redirect to the `raster` viewer

<p align="center">
  <img src="https://user-images.githubusercontent.com/10407788/146790933-e439893c-ef2e-4d78-a372-f2f18694836c.png"/>
  <p align="center">STAC Search viewer</p>
</p>

Code: [/runtime/eoapi/stac](https://github.com/developmentseed/eoAPI/tree/main/runtime/eoapi/stac)

---

### Raster Tiles

The dynamic tiler deployed within eoAPI is built on top of [titiler-pgstac](https://github.com/stac-utils/titiler-pgstac) and [pgstac](https://github.com/stac-utils/pgstac). It enables large scale mosaic based on results of STAC searches queries:

- Full **titiler-pgstac** implementation

<p align="center">
  <img src="https://user-images.githubusercontent.com/10407788/129632282-f71e9f45-264c-4882-af28-7062c4e56f25.png"/>
  <p align="center">TiTiler-PgSTAC workflow</p>
</p>

Code: [/runtime/eoapi/raster](https://github.com/developmentseed/eoAPI/tree/main/runtime/eoapi/raster)

---

### OGC Features / Vector Tiles

OGC Features + Tiles API built on top of [tipg](https://github.com/developmentseed/tipg).

By default, the API will look for tables in the `public` schema of the database. We've also added three custom functions which connect to the pgSTAC schema:

- **pg_temp.pgstac_collections_view**: Simple function which return PgSTAC Collections
- **pg_temp.pgstac_hash**: Return features for a specific searchId (hash)
- **pg_temp.pgstac_hash_count**: Return the number of items per geometry for a specific searchId (hash)

Code: [/runtime/eoapi/vector](https://github.com/developmentseed/eoAPI/tree/main/runtime/eoapi/vector)

---

## Project structure

```
 ├──demo/                  - items/collections and notebook
 ├──infrastructure/        - Deployment options (e.g AWS CDK application)
 └──runtime/eoapi/
    ├── raster/            - "eoapi.raster" python package
    ├── stac/              - "eoapi.stac" python package
    └── vector/            - "eoapi.vector" (OGC features + OGC tiles) python package
```

## Launch application locally

You can launch the APIs locally using docker. This will start 3 services: database, eoapi.stac, eoapi.raster
```
git clone https://github.com/developmentseed/eoAPI.git
cd eoAPI
docker-compose build
docker-compose up stac raster
```

Or install everything locally
```
python -m pip install --upgrade virtualenv
virtualenv .venv
source .venv/bin/activate

python -m pip install "psycopg[binary,pool]" uvicorn
python -m pip install runtime/eoapi/raster runtime/eoapi/stac runtime/eoapi/vector

export DATABASE_URL=postgresql://username:password@0.0.0.0:5439/postgis  # Connect to the database of your choice

# OGC Features/Tiles
.venv/bin/uvicorn eoapi.vector.app:app --port 8000 --reload

# Raster
.venv/bin/uvicorn eoapi.raster.app:app --port 8000 --reload

# STAC
.venv/bin/uvicorn eoapi.stac.app:app --port 8000 --reload
```

## Deployment

See [DEPLOYMENT.md](https://github.com/developmentseed/eoAPI/blob/main/infrastructure/DEPLOYMENT.md)

## Contribution & Development

See [CONTRIBUTING.md](https://github.com/developmentseed/eoAPI/blob/main/CONTRIBUTING.md)

## License

See [LICENSE](https://github.com/developmentseed/eoAPI/blob/main/LICENSE)

## Authors

Created by [Development Seed](<http://developmentseed.org>)

See [contributors](https://github.com/developmentseed/eoAPI/graphs/contributors) for a listing of individual contributors.

## Changes

See [CHANGES.md](https://github.com/developmentseed/eoAPI/blob/main/CHANGES.md).

SNCF Open Data API Documentation
================================

Version: v2.1  
Base URL: https://ressources.data.sncf.com/api/explore/v2.1/catalog

Overview
--------
This API provides programmatic access to SNCF's open datasets. It allows you to query, filter, and export datasets via RESTful endpoints.

Authentication
--------------
No authentication is required for public access.

Main Endpoints
==============

GET /datasets/{dataset_id}/records
-----------------------------------
Query dataset records.

**Path Parameters:**

- ``dataset_id`` *(string, required)*: Identifier of the dataset.

**Query Parameters (optional):**

- ``select``: Choose specific fields or compute expressions.
- ``where``: Filter using Opendatasoft Query Language (ODSQL).
- ``group_by``: Group results by a specific field or expression.
- ``order_by``: Sort results (e.g., `order_by=name asc`).
- ``limit`` *(integer)*: Max number of items to return (max 100 or 20,000 depending on grouping).
- ``offset`` *(integer)*: Index of the first result (for pagination).
- ``refine``: Filter using facet values (e.g., `refine=city:Paris`).
- ``exclude``: Exclude specific facet values.
- ``lang``: Language (default: "fr").
- ``timezone``: Timezone for datetime fields (e.g., "UTC").
- ``include_links`` *(boolean)*: Adds HATEOAS links if `true`.
- ``include_app_metas`` *(boolean)*: Includes application metadata if `true`.

**Example Response:**

.. code-block:: json

   {
     "total_count": 137611,
     "results": [
       {
         "name": "Saint-Leu",
         "coordinates": {
           "lat": 46.7306,
           "lon": 4.50083
         },
         "population": 29278,
         ...
       }
     ]
   }

GET /datasets/{dataset_id}/records/{record_id}
-----------------------------------------------
Fetch a single record from a dataset.

**Path Parameters:**

- ``dataset_id`` *(string, required)*
- ``record_id`` *(string, required)*

**Query Parameters (optional):** Same as in `/records`.

GET /datasets/{dataset_id}/exports
-----------------------------------
List available export formats for a dataset.

**Path Parameters:**

- ``dataset_id`` *(string, required)*

GET /datasets/{dataset_id}/exports/{format}
-------------------------------------------
Export a dataset in the specified format.

**Supported formats:**

- ``csv``, ``parquet``, ``gpx``, etc.

**Path Parameters:**

- ``dataset_id`` *(string, required)*
- ``format`` *(string, required)*

**Query Parameters:**

- Same as in `/records`, plus:
- ``use_labels`` *(boolean)*: Output field labels instead of field names.
- ``compressed`` *(boolean)*: Export as compressed (e.g., `.csv.gz`).
- ``epsg`` *(integer)*: EPSG projection code for geometric exports.

**Response:** Downloadable file in specified format.

GET /datasets/{dataset_id}/facets
----------------------------------
List values for each facet (for filtering/navigation).

**Query Parameters:**

- Same as in `/records`, plus:
- ``facet``: Field or facet expression (e.g., `facet=name` or `facet=facet(name="city", sort="-count")`).

**Example Response:**

.. code-block:: json

   {
     "facets": [
       {
         "name": "timezone",
         "facets": [
           {
             "name": "Europe",
             "count": 68888
           }
         ]
       }
     ]
   }

GET /datasets/{dataset_id}/attachments
---------------------------------------
List file attachments related to the dataset.

**Path Parameters:**

- ``dataset_id`` *(string, required)*

GET /datasets/{dataset_id}/exports/csv
---------------------------------------
Export a dataset in CSV format with extra CSV-specific parameters.

**Additional CSV Parameters:**

- ``delimit``: Field delimiter (e.g., `;`).
- ``list_separator``: Separator for multivalue fields (e.g., `,`).
- ``quote_all`` *(boolean)*: Quote all fields if `true`.
- ``with_bom`` *(boolean)*: Add BOM for Excel compatibility (default `true` in v2.1).

GET /datasets/{dataset_id}/exports/parquet
-------------------------------------------
Export a dataset in Parquet format.

**Additional Parquet Parameter:**

- ``parquet_compression``: Compression type (e.g., `snappy`).

GET /datasets/{dataset_id}/exports/gpx
---------------------------------------
Export a dataset in GPX format (for geographic data).

**Additional GPX Parameters:**

- ``name_field``: Field to use as GPX `name`.
- ``description_field_list``: Fields used for GPX `description`.
- ``use_extension`` *(boolean)*: Use `<extension>` tag (default: `true` in v2.1).

Response Codes
==============

- **200 OK**: Successful request.
- **400 Bad Request**: Invalid ODSQL query or parameters.
- **401 Unauthorized**: Authentication required.
- **429 Too Many Requests**: Rate limit exceeded.
- **500 Internal Server Error**: Server error.

**Example Error Response:**

.. code-block:: json

   {
     "message": "ODSQL query is malformed: invalid_function()",
     "error_code": "ODSQLError"
   }

Additional References
=====================

- API Console: https://ressources.data.sncf.com/api/explore/v2.1/console
- ODSQL Language Reference: https://docs.opendatasoft.com/en/data_exploration/04_analyzing_data/03_using_query_language.html
- Horaires Bus: https://horairesbus.github.io/ — This community-driven website offers useful tools and examples for exploring French public transportation schedules. It can be a complementary resource when using the SNCF Open Data API.

.. toctree::
   :maxdepth: 2
   :caption: Fetch and Filter

   practical-use/how-to-fetch-and-filter-real-time-train-station-data

.. toctree::
   :maxdepth: 2
   :caption: Exporting

   practical-use/exporting-sncf-datasets-to-csv,-parquet,-and-gpx

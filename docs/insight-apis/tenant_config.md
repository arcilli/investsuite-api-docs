---
title: Tenant Configuration Format
---

**The tenant configuration files are not user-editable.**
This page is for internal documentation purposes.

## Tenant Configuration Usage
The `{tenant_identifier}.json` files located in `/tenant_config` configure the following functionality:

- Universes under property `"UNIVERSES"`: Named instrument universes which are collections of instruments/indices that belong to a tenant-usecase.
  Each universe contains the allowed data sources with associated functionality.
- Allowed providers list under `"ALLOWED_PROVIDERS"`: Permissions the allowed data providers for the tenant.
- Classification mapping under `"CLASSIFICATION_MAPPING"`: Points to a mapping file per data provider from predefined categories -> instrument/data field identifiers.

## Data Model

A subsection per request type and subpath in the endpoint.

=== "Example tenant_config.json"

    ```JSON
    {
      "UNIVERSES": [
        {
          "NAME": "EXAMPLE_UNIVERSE_1",
          "DATA_SOURCES": {
            "MORNINGSTAR": [
              "REFERENCE",
              "COMPOSITION"
            ],
            "DATASTREAM": [
              "REFERENCE",
              "TIMESERIES",
              "FUNDAMENTAL"
            ]
          },
          "INSTRUMENTS": [
            "INSTRUMENT_ID_1"
            "INSTRUMENT_ID_2"
          ],
          "INDICES": [
            "INDEX_ID_1",
            "INDEX_ID_2"
          ]
        }
      ],
      "ALLOWED_PROVIDERS": [
        "MORNINGSTAR",
        "DATASTREAM"
      ],
      "CLASSIFICATION_MAPPING": "demo.com"
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`"UNIVERSES"` | A list of universe definition objects. Defines named instrument universes which are collections of instruments/indices that belong to a tenant-usecase. Each universe contains the allowed data sources for associated DataTypes. | `list[object]` | cf. above | Yes
├`"NAME"`| The name of the universe. Can be used in endpoints under the `universe_name` optional query parameter, by default this is not passed and these endpoints return the set of all instrument universes after lookup. | `str` | `EXAMPLE_UNIVERSE_1` | Yes
├`"DATA_SOURCES"`| Configures the allowed data provider source by name key and a list of datatypes.<br>• Available data sources: `["MORNINGSTAR", "MORNINGSTAR_ONDEMAND", "DATASTREAM", "CUSTOM", "RDP", "TRKD", "COINMARKETCAP"]`.<br>• Available DataTypes: `["REFERENCE", "TIMESERIES", "ESG", "ESG_TIMESERIES", "COMPOSITION", "COMPOSITION_TIMESERIES", "FUNDAMENTAL"]` | `object[str, list[DataType]]` | `{"MORNINGSTAR": ["REFERENCE", "COMPOSITION"], "DATASTREAM": ["REFERENCE", "TIMESERIES", "FUNDAMENTAL"]}` | No, can be empty `{}`
├`"INSTRUMENTS"`| A list of instrument identifiers. | `list[str]` | `["INSTRUMENT_ID_1", "INSTRUMENT_ID_2"]` | No
└`"INDICES"`| A list of indices/composition-of-instruments identifiers. | `str` | `["INDEX_ID_1", "INDEX_ID_2"]` | No
`"ALLOWED_PROVIDERS"` | A list of allowed data provider names. Permissions the allowed data providers for the tenant.<br>• Available providers: `["MORNINGSTAR", "MORNINGSTAR_ONDEMAND", "DATASTREAM", "CUSTOM", "RDP", "TRKD", "COINMARKETCAP"]` | `list[str]` | cf. above | Yes
`"CLASSIFICATION_MAPPING"` | Points to the filename of a json map in the `/classification_mapping/` folder. This configures the mappings per data provider of from predefined categories -> instrument/data field identifiers (e.g., `["REBRP-EquityRegionEuropeexeuro", "REBRP-EquityRegionEurozone", "REBRP-EquityRegionUnitedKingdomLongRescaled"]` are data field lookup keys for the `EUROPE_DEVELOPED` subcategory for `EQUITY_REGION` category for `MORNINGSTAR` data provider). | `list[str]` | cf. above | Yes

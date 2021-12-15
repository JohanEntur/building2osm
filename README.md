# building2osm

Generates import files for OpenStreetMap with buliding footprints from Kartverket.

TEST

### building2osm

Generates geojson import file with building footprints.

_Note: The WFS service used is currently not delivering building polygons._

Usage: `python3 building2osm.py <municipality> [-split] [-original] [-verify] [-debug]`

Parameters:

* _municipality_ - Name of the municipality to generate. Output for several municipalities is generated if county name or "Norway" is given.
* `-split` - Also split output file into smaller subdivisions ("bydel" or post districts).
* `-original` - Produce file without any modifications.
* `-verify` - Include extra tags for verification of topology modificatons.
* `-debug` - Include extra tags for debugging.

### building\_merge

Conflates the geojson import file with existing buildings in OSM and produces an OSM file for manual verification and uploading.

Usage: `python3 building_merge.py <municipality> [<max distance>] [<filename.geojson>] [-debug]`

Parameters:

* _municipality_ - Name of the municipality to conflate.
* _max distance_ - Optional maximum Hausdorff distance between matched buildings. Default value is 10 metres (5 metres is the building is tagged).
* _filename.geojson_ - Optional input file in geojson format. If not specified, the import file for the municipality will be loaded (it must be present in the default folder or in a predefined folder).
* `-debug` - Include extra tags for debugging.

### municipality\_split

Splits the geojson import file into smaller subdivisions such as post districts or "bydel".

Usage: `python3 municipality_split.py <municipality> [ --subdivision [ bydel | postnummer ]`

Parameter:

* _municipality_ - Name of the municipality to split.
* `--subdivision bydel` - Split municipality according to boroughs.
* `--subdivision bydel` - Split municipality according to post districts (please check for leftover buildings if post districts do not correspond exactly to municipality boundaries).

### Notes

* Source data is from the Cadastral registry of Kartverket
  * "INSPIRE Buildings Core2d" - Contains polygons of the building footprints.
  * "Matrikkelen Building point" - Contains information about the building type/usage.
  * "Matrikkelen Address apartment level" - Contains information about levels of the building.
* The building=\* tag is given a value corresponding to the _building\_type_ translation table in this respository. Please provide feedback if you observe that the tagging should be modified.
* Certain modifications of the footprint polygons are made to avoid clutter in OSM:
  * Polygons which are almost square are rectified (orthogonalized) to get exact 90 degrees corners. Groups of connected buildings are rectified as a group. Multipolygons are supported. A polygon is not rectified if it would relocate one of its nodes by more than 20 centimeters.
  * Redundant nodes are removed if they are located on an (almost) straight line.
  * Curved walls are only simplified lightly.
* Output is stored in a geosjon file which may be loaded into JOSM when the OpenData plugin has been installed. Please read the [import plan](https://wiki.openstreetmap.org/wiki/Import/Catalogue/Norway\_Building\_Import) for guiding on how to do the import.
* The _building\_merge_ program conflates the import buildings with existing buildings in OSM.
  * New buildings are loaded from the geojson import file.
  * Existing buildings are loaded from OSM.
  * New and existing buildings which are each other's best match within the given maximum Hausdorff distance (default 10 metres) are automatically conflated. They also need to have similar size (default 50% difference).
  * The _OSM\_BUILDING_ tag will show which building tag was used by the existing building, unless they are in similar residential/commercial/farm categories.
  * Use the To-do plugin in JOSM to:
    1. Resolve _Overlapping buildings_.
    2. Resolve _Building within buildings_.
    3. Check _OSM\_BUILDING_ for manual retagging of building types.
    4. Check untouched existing OSM buildings.
    5. Check if entrances or other tagged nodes needs to be reconnected to the new buildings (search for `type:node ways:0 -untagged`).

### References

* [Kartverket product specificaton](https://register.geonorge.no/data/documents/Produktspesifikasjoner\_Matrikkelen%20-%20Bygningspunkt\_v1\_produktspesifikasjon-matrikkelen-bygningspunkt-versjon20180501\_.pdf)
* [OSM Norway building import plan](https://wiki.openstreetmap.org/wiki/Import/Catalogue/Norway\_Building\_Import)
* [Building import progress](https://wiki.openstreetmap.org/wiki/Import/Catalogue/Norway\_Building\_Import/Progress)

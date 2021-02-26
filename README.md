# building2osm
Generates import files for OpenStreetMap with buliding footprints from Kartverket

#### Usage

<code>python3 building2osm.py [municipality] [-original] [-verify] [-debug]</code>

Parameters:
* _municipality_ - Name of the municipality to generate.
* <code>-original</code> - Produce file without any modifications.
* <code>-verify</code> - Include extra tags for verification of topology modificatons.
* <code>-debug</code> - Include extra tags for debugging.

#### Notes
* Source data is from the Cadastral registry of Kartverket
  * "INSPIRE Buildings Core2d" - Contains polygons of the building footprints.
  * "Matrikkelen Bygningspunkt" - Contains information about the building type/usage.
* The building=* tag is given a value corresponding to the _building_type_ translation table in this respository. Please provide feedback if you observe that the tagging should be modified. 
* The program makes certain modifications of the footprint polygons to avoid clutter in OSM:
  * Polygons which are almost square are rectified (orthogonalized) to get exact 90 degrees corners. Groups of connected buildings are rectified as a group. Multipolygons are cupported. A polygon is not rectified if it would relocate one of its nodes by more than 20 centimeters.
  * Redundant nodes are removed if they are located on an (almost) straight line.
  * Curved walls are being preserved.
* Output is stored in a geosjon file which may be loaded into JOSM when the OpenData plugin has been installed. Please read the [mport plan](https://wiki.openstreetmap.org/wiki/Import/Catalogue/Norway_Building_Import) for guiding on how to do the import.

#### References

* [Kartverket product specificaton](https://register.geonorge.no/data/documents/Produktspesifikasjoner_Matrikkelen%20-%20Bygningspunkt_v1_produktspesifikasjon-matrikkelen-bygningspunkt-versjon20180501_.pdf)
* [OSM Norway building import plan](https://wiki.openstreetmap.org/wiki/Import/Catalogue/Norway_Building_Import)
* [Building import progress](https://wiki.openstreetmap.org/wiki/Import/Catalogue/Norway_Building_Import/Progress)

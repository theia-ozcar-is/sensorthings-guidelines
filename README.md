
# OGC SensorThings API / Theia OZCAR pivot model mapping guideline
---

## Specification of the OGC SensorThings API

The OGC SensorThings API data model consists of two parts: (1) the Sensing part and (2) the Tasking part. The Sensing part allows IoT devices and applications to CREATE, READ, UPDATE, and DELETE (i.e., HTTP POST, GET, PATCH, and DELETE) IoT data and metadata in a SensorThings service.
Reference: [http://www.opengeospatial.org/standards/sensorthing](ttp://www.opengeospatial.org/standards/sensorthing)

### SensorThings API part 1 : the Sensing Entities
OGC SensorThings API Part 1: Sensing Version 1.0: http://docs.opengeospatial.org/is/15-078r6/15-078r6.html
OGC SensorThings API Part 1: Sensing Version 1.1: https://portal.ogc.org/files/92752

The Sensing part is designed based on the ISO/OGC Observation and Measurement (O&M) model [OGC 10-004r3 and ISO 19156:2011]. The key to the model is that an Observation is modeled as an act that produces a result whose value is an estimate of a property of the observation target or FeatureOfInterest. An Observation instance is classified by its event time (e.g., resultTime and phenonmenonTime),FeatureOfInterest, ObservedProperty, and the procedure used (often a Sensor).
Things are also modeled in the SensorThings API, and its definition follows the ITU-T definition: “an object of the physical world (physical things) or the information world (virtual things) that is capable of being identified and integrated into communication networks” [ITU-T Y.2060].
#### Entities Model
A Datastream is a collection of Observations grouped by the same ObservedProperty and Sensor. An Observation is an event performed by a Sensor that produces a result whose value is an estimate of an ObservedProperty of the FeatureOfInterest. A Thing’s Location may be identical to the Thing’s Observations’ FeatureOfInterest. In the context of the IoT, the principle location of interest is usually associated with the location of the Thing, especially for in-situ sensing applications. For example, the location of interest of a wifi-connected thermostat should be the building or the room in which the smart thermostat is located. And the FeatureOfInterest of the Observations made by the thermostat (e.g., room temperature readings) should also be the building or the room. In this case, the content of the smart thermostat’s location should be the same as the content of the temperature readings’ feature of interest. However, the ultimate location of interest of a Thing is not always the location of the Thing (e.g., in the case of remote sensing). In those use cases, the content of a Thing’s Location is different from the content of the FeatureOfInterest of the Thing’s Observations.For the Things whose location changed, the HistoricalLocations entities offer the history of the Thing’s locations. A Thing also can have multiple Datastreams.

#### SensorThings 1.0 model

![SensorThingsUML1.0][UML1]

#### SensorThings 1.1 model

All entities (except for HistoricalLocation) now have a field of the type JSON Object. In version 1.0 such a field already existed for the Thing entity (named properties), and for the Observation entity (named parameters). These fields proved to be extremely useful for storing additional structured meta data used in many domains, that could also be used in filters. Now, all entities except for HistoricalLocation have such a very useful property, whereby in the Observation entity it retains the name parameters.

#### MultiDatastream (in both version 1.0 & 1.1)

SensorThings’ MultiDatastream entity is an extension to handle complex observations when the result is an array.

#### Common Control Information

In SensorThings control information is represented as annotations whose names start with iot followed by a dot ( . ). Annotations are name/value pairs that have a dot ( . ) as part of the name.
When annotating a name/value pair for which the value is represented as a JSON object, each annotation is placed within the object and represented as a single name/value pair.
In SensorThings the name always starts with the "at" sign ( @ ), followed by the namespace iot , followed by a dot ( . ), followed by the name of the term (e.g., "@iot.id":1 ).

Annotation | Definition | Data type | Multiplicity and use 
--- | --- | --- | ---
id|id is the system-generated identifier of an entity. id is unique among the entities of the same entity type in a SensorThings service.|Any|One (mandatory)
selfLink|selfLink is the absolute URL of an entity that is unique among all other entities.|URL|One (mandatory)
navigationLink|navigationLink is the relative or absolute URL that retrieves content of related entities.|URL|One-to-many (mandatory)

## External resources

### EndPoint examples

* Données hydrométrie france (SCHAPI) https://iddata.eaufrance.fr/api/stapiHydrometry
* French surface water quality :    https://sensorthings-wq.brgm-rec.fr/FROST-Server/v1.0
* SensorThings 1.0 GroundWater BRGM : https://sensorthings.brgm-rec.fr/SensorThingsGroundWater/
* Hydrométrie France : https://sta4hydrometry.brgm-rec.fr/FROST-Server/v1.1
* AGRHYS:http://sensorthings.geosas.fr/

### References: 
* Tools to manipulate AGHRYS endpoint: http://sensorthings.geosas.fr/Query - https://github.com/Mario-35/api-sensorthing/blob/main/doc/agrhys.md
* Extending INSPIRE to the Internet of Things through SensorThings API : https://www.mdpi.com/2076-3263/8/6/221
* API4INSPIRE: https://datacoveeu.github.io/API4INSPIRE/sensorthingsapi/1_Home.html

### Mapping between SensorThings and Theia/OZCAR pivot model

Theia/OZCAR pivot model provided by producer before to be denomarlised and inserted into the database : https://theia-ozcar.gricad-pages.univ-grenoble-alpes.fr/doc-producer/_downloads/03e6d970af026bceba4551f485518519/ClassDiagramPivotFormatV1.1.pdf

Beware that "Observation" of in the pivot model really is more like a Datastream in SensorThing ! With the constraints defined by our Pivot model, We will have - 1 to 1 relations between Datastream, Thing, Location, ObservedProperty and Sensor.
Datasets with multiple additional values ((examples: uncertainties, errors, parameter values related to instrumentation...) may be mapped to MultiDatastreams.
Specific properties and mapping can be “documented” in the serverSettings object returned when a GET is sent to the endpoint. See also serverSettings.conformance.
In Theia/OZCAR observation data, sensor can change along time for a same ObservedProperty. A different datastream will have to be considered related to the same thing and the same ObservedProperty.

#### Datastream

Examples:
* BRGM: SensorThingsGroundWater BRGM Datastream: https://sensorthings.brg…ater/v1.0/Datastreams(1)
* BRGM hydrométrie France : https://sta4hydrometry.brgm-rec.fr/FROST-Server/v1.1/Sensors(2)/Datastreams

M = Mandatory, O = optional

Datastream sensorThings | Observation -Theia/OZCAR | Notes
--- | --- | ---
| Datastream.name M | flux SensorThings in: no mapping, flux sensor SensorThings out: to be constructed using other fields | Ex 1 BRGM: Datastream.name: GroundWater depth / Installation de suivi eau souterraine quantité Ex 2 BRGM Datastream.name: Water flow at [Barbotteau] à Petit-Bourg - Barbotteau with method Water flow measurement by electronic probe |
| Datastream.description M | flux SensorThings in: no mapping, flux sensor SensorThings out: to be constructed using other fields | Ex 2 BRGM Datastream.description : Water flow at [Barbotteau] à Petit-Bourg - Barbotteau with method Water flow measurement by electronic probe |
| Datastream.ObservationType M | | Ex 1 & 2 BRGM observationType: http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_Measurement |
| Datastream.unitOfMeasurement M | ObservedProperty.unit M | Ex 1 & 2 BRGM observationType:http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_Measurement |
| Datastream.observedArea O | | ObservedArea is a bbox (envelope) |
| Datastream.resultTime O | Observation.TemporalExtent M | |
| Datastream.phenomenonTime O | | No notion of phenomenonTime in Theia/OZCAR |
| Datastream.@iot.id M, Datastream.@iot.selfLink M | Observation.observationId M | Use @iot.id or @iot.selfLink to give an unique observationId Ex 1 BRGM @iot.id:1 @iot.selfLink:https://sensorthings.brgm-rec.fr/SensorThingsGroundWater/v1.0/Datastreams(1) |
| Datastream.properties.processingLevel | Observation.processingLevel:EnumProcessingLevel O | |
||Observation.dataType:EnumDataTypes=Numeric M|Theia/OZCAR will impose the value|
||Observation.timeSerie=true M |Theia/OZCAR will impose the value|

#### MultiDatastream

The multidatastream looks similar to the usual Datastream, with these differences. A MultiDatastream may also have multiple ObservedProperties.
||||
--- | --- | ---
ObservationType M |  | always OM_ComplexObservation
multiObservationDataTypes M |  | array of valueCodes

#### ObservedProperty

Examples:
* SensorThingsGroundWater BRGM ObservedProperty: https://sensorthings.brgm-rec.fr/SensorThingsGroundWater/v1.0/Datastreams(1)/ObservedProperty
* https://sta4hydrometry.brgm-rec.fr/FROST-Server/v1.1/Datastreams(2)/ObservedProperty

ObservedProperty - SensorThings | ObservedProperty - Theia/OZCAR | Notes
--- | --- | ---
ObservedProperty.name M	| observedProperty.name M	| Ex 1 BRGM: ObservedProperty.name:GroundWater depth Ex 2 : ObservedProperty.name :Water flow
ObservedProperty.description M	| observedProperty.description O | Ex 1 BRGM: ObservedProperty.description:GroundWater depth Ex 2 : ObservedProperty.description:Water flow
ObservedProperty.definition M | ObservedProperty.theiaCategories M | semantic links should be created between resources in order to fill theia/ozcar custom fields (such as theiaCategories). 
Datastream.unitOfMeasurement M |	ObservedProperty.unit M |

#### Things

Examples:
* SensorThingsGroundWater BRGM Thing: https://sensorthings.brgm-rec.fr/SensorThingsGroundWater/v1.0/Datastreams(1)/Thing
* https://sta4hydrometry.brgm-rec.fr/FROST-Server/v1.1/Datastreams(2)/Thing

Things - SensorThings | FeatureOfInterest - Theia/OZCAR | Notes
--- | --- | ---
Thing.name M |	FeatureOfInterest.samplingFeature.name M |	Thing.name is the a name of a network of station. Theia/OZCAR :FeatureOfInterest.samplingFeature.name is the name of the station. It is suggested to implement the finest granularity of  station
Thing.description M	||	Ex BRGM: Thing.description:Implantation station 2m avant prise d'eau Conseil Général
Thing.properties.id	| not implemented yet: FeatureOfInterest.samplingFeature.uri ??? |	Thing.properties.id is an URI .
||FeatureOfInterest.samplingFeature.type=Feature M|
Thing.Locations.[*].location.coordinates M |	FeatureOfInterest.samplingFeature.geometry.coordinates M |	https://sta4hydrometry.brgm-rec.fr/FROST-Server/v1.1/Things(1)/Locations
Thing.Locations.location.type M	| |Theia/OZCAR: FeatureOfInterest.samplingFeature.geometry.type=Point,LineString,Polygon,MultiPoint, MultiLineString,MultiPolygon FeatureOfInterest.samplingFeature.geometry.type must be deducted using the different location.type of the locations array

### Location
Examples:
*SensorThingsGroundWater BRGM Location : https://sensorthings.brgm-rec.fr/SensorThingsGroundWater/v1.0/Things(1)/Locations

Location  - SensorThings |  Theia/OZCAR | Notes
--- | --- | ---
Location.name M	||	Ex BRGM: Location.name: Installation de suivi eau souterraine quantité
Location.description M	||	Ex BRGM : Location.description: Installation de suivi eau souterraine quantité
Location.encodingType M (GeoJSON)	||	The encoding type of the Locationproperty. Its value is one of the ValueCode enumeration, ie application/vnd.geo+json
location.type M	||	The location type is defined by encodingType. Ex:Location.location.geometry.type:Point FeatureOfInterest.samplingFeature.geometry.type : Must be deducted using the different location.type of the locations array of a thing
| location.coordinates M ||

### Location
Examples:
* SensorThingsGroundWater BRGM Sensor : https://sensorthings.brg….0/Datastreams(1)/Sensor
* https://sta4hydrometry.brgm-rec.fr/FROST-Server/v1.1/Sensors(2)

Sensor - SensorThings |  Procedure - Theia/OZCAR | Notes
--- | --- | ---
Sensor.name M |	Procedure.dataProduction.sensors[0].sensorType O |
Sensor.description M |	Procedure.dataProduction.method O | BRGM: Sensor.description: Groundwater depth measurement by electronic probe BRGM Sensor.description:Water flow measurement by electronic probe
Sensor.encodingType ||The encoding type of the metadata property. Its value is: of the ValueCode enumeration, ie application/pdf  or  http://www.opengis.net/doc/IS/SensorML/2.0
Sensor.metadata M ||The detailed description of the Sensor or system. The metadata type is defined by encodingType . BRGM: Sensor.metadata: https://data.geoscience.fr/ncl/Proc/70
Sensor.properties.lineageInformations | Procedure.lineageInformations O, Procedure.lineageInformation.processingDescription O, procedure.lineageInformation.processingDate O
Sensor.properties[] ? | Procedure.Sensors O |

### Observation
Examples:
* SensorThingsGroundWater BRGM Observations : https://sensorthings.brgm-rec.fr/SensorThingsGroundWater/v1.0/Observations

Observation - SensorThings |  Result - Theia/OZCAR | Notes
--- | --- | ---
Observation.phenomenonTime M||		
Observation.resultTime M| DataFile Content: dateEnd	|
Observation.result M|	DataFile Content: value	|
Observation.resultQuality O | Result.QualityFlag.code O (M if there is quality flags) | Describes the quality of the result . DQ_Element cf: https://sensorthings.brgm-rec.fr/SensorThingsGroundWater/v1.0/Observations “nameOfMeasure” is not always a name of element like DQ_Status. it is free text.
Observation.validTime O||
Observation.parameters O||Key-value pairs showing the environmental conditions during measurement. NamedValues in a JSON
Observation.parameters.source.file | Result.dataFile  M | Observation.parameters.source.file:piezoaqi/SEBA/07542X0042_20200618_033300.txt
Datastream.properties.missingValue	|Result.missingValue O|
n these case of additional values linked to the observedProperty (ex: uncertainties, error), need to use MultiDataStream instead of DataStream | Result.additionalValues O

### Observation
Examples:
* SensorThingsGroundWater BRGM FeatureOfInterest : https://sensorthings.brgm-rec.fr/SensorThingsGroundWater/v1.0/Observations(1)/FeatureOfInterest

The FeatureOfInterest SensorThings is only useful for retrieving measurement coordinates from Theia/OZCAR timeseries in csv data files.

FeatureOfInterest  - SensorThings |  Theia/OZCAR | Notes
--- | --- | ---
FeatureOfInterest.name M||BRGM: FeatureOfInterest.name : FoI for location 6998
FeatureOfInterest.description M	||	BRGM : FeatureOfInterest.description : Generated from location 6998
FeatureOfInterest.encodingType M (GeoJSON)|| The encoding type of the feature property. Its value is one of the ValueCode enumeration, ie application/vnd.geo+json
FeatureOfInterest.feature.type M || The FeatureOfInterest type is defined by encodingType . ex: FeatureOfInterest.feature.geometry.type:Point
FeatureOfInterest.feature.coordinates M	|| Result DataFile Content: longitude,latitude


[UML1]:  https://upload.wikimedia.org/wikipedia/commons/thumb/5/57/SensorThings_API_data_model.svg/1920px-SensorThings_API_data_model.svg.png


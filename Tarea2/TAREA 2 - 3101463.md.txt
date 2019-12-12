## Cuál es el problema a tratar?

Se desea saber cuántos hallazgos arqueológicos se han identificado en todo el territorio nacional por departamento, para tener en cuenta que entidad gubernamental tiene un mayor accionamiento u actividad en tratar estos tipos de registros ya que la Constitución Política de Colombia, 1991 Artículo 63 especifica que los bienes de uso público, los parques naturales, las tierras comunales de grupos étnicos, las tierras de resguardo, el patrimonio arqueológico de la Nación y los demás bienes que determine la ley son inalienables, imprescriptibles e inembargables.

## Por qué la publicación de servicios OGC puede ayudar a resolverlo?

Con los registros identificados geográficamente es de conocimiento saber en qué lugar se encuentran localizados e identificados los diferentes asentamientos, adicional cada departamento según la cantidad de hallazgos es de tener un grupo numeroso de profesionales enfocados a la tarea de estudio de los grupos identificados adicional al aporte del gobierno central en implementación de tecnologías para que ayude al intercambio de información de igual forma este tipo de información es de conocimiento de todas las entidades competentes estén al tanto.

## Qué servicios propone para la solución de su problema? WMS? WMTS? WFS? Por qué ?

La incorporacion de servicios WMTS ya que son muchos puntos identificados a lo largo del territorio nacional que hasta el momento se tiene 6599, por medio de teselas el rendimiento de presentacion del mapa es eficiente. 


## Descripción de los datos seleccionados (Origen, descripción, características especiales, atributos, url para descarga)

El ICANH, mediante el Grupo de Arqueología y sus otras dependencias asesora a las autoridades locales, regionales, distritales y municipales en la difícil labor de preservar los yacimientos arqueológicos y los bienes muebles extraídos de esos sitios y que conforman el Patrimonio Arqueológico de la Nación

__Atributos Tabla - u2_sitarq_anla_wgs84__

![img2](IMAGENES/tabla_sitios_arq.JPG)

Fuente: https://www.icanh.gov.co/

## Descripción del procesamiento realizado con postgis (Incluir los sqls)

__Subimos las capas con el administrador de bases de datos que tiene el Qgis.__

![img2](IMAGENES/subir_capas_postgres.JPG)

1. Se crea una capa o tabla espacial en postgis con el siguiente SQL para saber cuántos hallazgos se encontraron en cada departamento:

```sql
create table u2_dept_arq as (
select id, departamento, cuenta, codigo, geom 
from(
select u2_dept.nombre departamento, count(u2_dept.nombre) cuenta
from u2_sitarq_anla_wgs84 u2_sit,u2_departamentos u2_dept
where ST_Within(u2_sit.geom,u2_dept.geom)
group by departamento) unir
join u2_departamentos
on unir.departamento = u2_departamentos.nombre);
```

2. Se genera un indice espacial a todas las tablas creadas, en este caso el que se acaba de crear:

```sql
CREATE INDEX sidx_u2deptarq_geom 
ON u2_dept_arq USING GIST (geom);
```

## Descripción de la forma en que creó la simbología (incluir los sld's y css)
1. En Qgis definimos la simbologia del shape, ingresando a propiedades

![img2](IMAGENES/simbologia_qgis.JPG)

2. Al estar ya configurado guardamos el estilo como SLD

![img2](IMAGENES/guardar_estilo.jpg)

![img2](IMAGENES/Estilo_SLD.JPG)

__Estilo Departamentos__

```xml
<?xml version="1.0" encoding="UTF-8"?>
<StyledLayerDescriptor xmlns="http://www.opengis.net/sld" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1.0" xmlns:se="http://www.opengis.net/se" xsi:schemaLocation="http://www.opengis.net/sld http://schemas.opengis.net/sld/1.1.0/StyledLayerDescriptor.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ogc="http://www.opengis.net/ogc">
  <NamedLayer>
    <se:Name>u2_departamentos</se:Name>
    <UserStyle>
      <se:Name>u2_departamentos</se:Name>
      <se:FeatureTypeStyle>
        <se:Rule>
          <se:Name>Single symbol</se:Name>
          <se:LineSymbolizer>
            <se:Stroke>
              <se:SvgParameter name="stroke">#674310</se:SvgParameter>
              <se:SvgParameter name="stroke-width">2</se:SvgParameter>
              <se:SvgParameter name="stroke-linejoin">bevel</se:SvgParameter>
              <se:SvgParameter name="stroke-linecap">square</se:SvgParameter>
            </se:Stroke>
          </se:LineSymbolizer>
        </se:Rule>
        <se:Rule>
          <se:TextSymbolizer>
            <se:Label>
              <ogc:PropertyName>nombre</ogc:PropertyName>
            </se:Label>
            <se:Font>
              <se:SvgParameter name="font-family">Arial</se:SvgParameter>
              <se:SvgParameter name="font-size">11</se:SvgParameter>
            </se:Font>
            <se:LabelPlacement>
              <se:PointPlacement>
                <se:AnchorPoint>
                  <se:AnchorPointX>0</se:AnchorPointX>
                  <se:AnchorPointY>0.5</se:AnchorPointY>
                </se:AnchorPoint>
              </se:PointPlacement>
            </se:LabelPlacement>
            <se:Fill>
              <se:SvgParameter name="fill">#000000</se:SvgParameter>
            </se:Fill>
            <se:VendorOption name="maxDisplacement">1</se:VendorOption>
          </se:TextSymbolizer>
        </se:Rule>
      </se:FeatureTypeStyle>
    </UserStyle>
  </NamedLayer>
</StyledLayerDescriptor>
```

__Estilo Departamentos con la cantidad de Hayazgos__

```xml
<?xml version="1.0" encoding="UTF-8"?>
<StyledLayerDescriptor xmlns="http://www.opengis.net/sld" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1.0" xmlns:se="http://www.opengis.net/se" xsi:schemaLocation="http://www.opengis.net/sld http://schemas.opengis.net/sld/1.1.0/StyledLayerDescriptor.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ogc="http://www.opengis.net/ogc">
  <NamedLayer>
    <se:Name>u2_dept_arq</se:Name>
    <UserStyle>
      <se:Name>u2_dept_arq</se:Name>
      <se:FeatureTypeStyle>
        <se:Rule>
          <se:Name>2 - 10</se:Name>
          <se:Description>
            <se:Title>2 - 10</se:Title>
          </se:Description>
          <ogc:Filter xmlns:ogc="http://www.opengis.net/ogc">
            <ogc:And>
              <ogc:PropertyIsGreaterThanOrEqualTo>
                <ogc:PropertyName>cuenta</ogc:PropertyName>
                <ogc:Literal>2</ogc:Literal>
              </ogc:PropertyIsGreaterThanOrEqualTo>
              <ogc:PropertyIsLessThanOrEqualTo>
                <ogc:PropertyName>cuenta</ogc:PropertyName>
                <ogc:Literal>10</ogc:Literal>
              </ogc:PropertyIsLessThanOrEqualTo>
            </ogc:And>
          </ogc:Filter>
          <se:PolygonSymbolizer>
            <se:Fill>
              <se:SvgParameter name="fill">#d7191c</se:SvgParameter>
            </se:Fill>
            <se:Stroke>
              <se:SvgParameter name="stroke">#232323</se:SvgParameter>
              <se:SvgParameter name="stroke-width">1</se:SvgParameter>
              <se:SvgParameter name="stroke-linejoin">bevel</se:SvgParameter>
            </se:Stroke>
          </se:PolygonSymbolizer>
        </se:Rule>
        <se:Rule>
          <se:Name>10 - 25</se:Name>
          <se:Description>
            <se:Title>10 - 25</se:Title>
          </se:Description>
          <ogc:Filter xmlns:ogc="http://www.opengis.net/ogc">
            <ogc:And>
              <ogc:PropertyIsGreaterThan>
                <ogc:PropertyName>cuenta</ogc:PropertyName>
                <ogc:Literal>10</ogc:Literal>
              </ogc:PropertyIsGreaterThan>
              <ogc:PropertyIsLessThanOrEqualTo>
                <ogc:PropertyName>cuenta</ogc:PropertyName>
                <ogc:Literal>25</ogc:Literal>
              </ogc:PropertyIsLessThanOrEqualTo>
            </ogc:And>
          </ogc:Filter>
          <se:PolygonSymbolizer>
            <se:Fill>
              <se:SvgParameter name="fill">#fdae61</se:SvgParameter>
            </se:Fill>
            <se:Stroke>
              <se:SvgParameter name="stroke">#232323</se:SvgParameter>
              <se:SvgParameter name="stroke-width">1</se:SvgParameter>
              <se:SvgParameter name="stroke-linejoin">bevel</se:SvgParameter>
            </se:Stroke>
          </se:PolygonSymbolizer>
        </se:Rule>
        <se:Rule>
          <se:Name>25 - 45</se:Name>
          <se:Description>
            <se:Title>25 - 45</se:Title>
          </se:Description>
          <ogc:Filter xmlns:ogc="http://www.opengis.net/ogc">
            <ogc:And>
              <ogc:PropertyIsGreaterThan>
                <ogc:PropertyName>cuenta</ogc:PropertyName>
                <ogc:Literal>25</ogc:Literal>
              </ogc:PropertyIsGreaterThan>
              <ogc:PropertyIsLessThanOrEqualTo>
                <ogc:PropertyName>cuenta</ogc:PropertyName>
                <ogc:Literal>45</ogc:Literal>
              </ogc:PropertyIsLessThanOrEqualTo>
            </ogc:And>
          </ogc:Filter>
          <se:PolygonSymbolizer>
            <se:Fill>
              <se:SvgParameter name="fill">#ffffbf</se:SvgParameter>
            </se:Fill>
            <se:Stroke>
              <se:SvgParameter name="stroke">#232323</se:SvgParameter>
              <se:SvgParameter name="stroke-width">1</se:SvgParameter>
              <se:SvgParameter name="stroke-linejoin">bevel</se:SvgParameter>
            </se:Stroke>
          </se:PolygonSymbolizer>
        </se:Rule>
        <se:Rule>
          <se:Name>45 - 125</se:Name>
          <se:Description>
            <se:Title>45 - 125</se:Title>
          </se:Description>
          <ogc:Filter xmlns:ogc="http://www.opengis.net/ogc">
            <ogc:And>
              <ogc:PropertyIsGreaterThan>
                <ogc:PropertyName>cuenta</ogc:PropertyName>
                <ogc:Literal>45</ogc:Literal>
              </ogc:PropertyIsGreaterThan>
              <ogc:PropertyIsLessThanOrEqualTo>
                <ogc:PropertyName>cuenta</ogc:PropertyName>
                <ogc:Literal>125</ogc:Literal>
              </ogc:PropertyIsLessThanOrEqualTo>
            </ogc:And>
          </ogc:Filter>
          <se:PolygonSymbolizer>
            <se:Fill>
              <se:SvgParameter name="fill">#abdda4</se:SvgParameter>
            </se:Fill>
            <se:Stroke>
              <se:SvgParameter name="stroke">#232323</se:SvgParameter>
              <se:SvgParameter name="stroke-width">1</se:SvgParameter>
              <se:SvgParameter name="stroke-linejoin">bevel</se:SvgParameter>
            </se:Stroke>
          </se:PolygonSymbolizer>
        </se:Rule>
        <se:Rule>
          <se:Name>123 - 1747</se:Name>
          <se:Description>
            <se:Title>123 - 1747</se:Title>
          </se:Description>
          <ogc:Filter xmlns:ogc="http://www.opengis.net/ogc">
            <ogc:And>
              <ogc:PropertyIsGreaterThan>
                <ogc:PropertyName>cuenta</ogc:PropertyName>
                <ogc:Literal>125</ogc:Literal>
              </ogc:PropertyIsGreaterThan>
              <ogc:PropertyIsLessThanOrEqualTo>
                <ogc:PropertyName>cuenta</ogc:PropertyName>
                <ogc:Literal>1747</ogc:Literal>
              </ogc:PropertyIsLessThanOrEqualTo>
            </ogc:And>
          </ogc:Filter>
          <se:PolygonSymbolizer>
            <se:Fill>
              <se:SvgParameter name="fill">#2b83ba</se:SvgParameter>
            </se:Fill>
            <se:Stroke>
              <se:SvgParameter name="stroke">#232323</se:SvgParameter>
              <se:SvgParameter name="stroke-width">1</se:SvgParameter>
              <se:SvgParameter name="stroke-linejoin">bevel</se:SvgParameter>
            </se:Stroke>
          </se:PolygonSymbolizer>
        </se:Rule>
      </se:FeatureTypeStyle>
    </UserStyle>
  </NamedLayer>
</StyledLayerDescriptor>
```

__Estilo de los puntos localizados de los Hallazgos__

```xml
<?xml version="1.0" encoding="UTF-8"?>
<StyledLayerDescriptor xmlns="http://www.opengis.net/sld" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1.0" xmlns:se="http://www.opengis.net/se" xsi:schemaLocation="http://www.opengis.net/sld http://schemas.opengis.net/sld/1.1.0/StyledLayerDescriptor.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ogc="http://www.opengis.net/ogc">
  <NamedLayer>
    <se:Name>u2_sitarq_anla_wgs84</se:Name>
    <UserStyle>
      <se:Name>u2_sitarq_anla_wgs84</se:Name>
      <se:FeatureTypeStyle>
        <se:Rule>
          <se:Name>Single symbol</se:Name>
          <se:PointSymbolizer>
            <se:Graphic>
              <se:Mark>
                <se:WellKnownName>circle</se:WellKnownName>
                <se:Fill>
                  <se:SvgParameter name="fill">#db1e2a</se:SvgParameter>
                </se:Fill>
                <se:Stroke>
                  <se:SvgParameter name="stroke">#801119</se:SvgParameter>
                  <se:SvgParameter name="stroke-width">1</se:SvgParameter>
                </se:Stroke>
              </se:Mark>
              <se:Size>7</se:Size>
            </se:Graphic>
          </se:PointSymbolizer>
        </se:Rule>
      </se:FeatureTypeStyle>
    </UserStyle>
  </NamedLayer>
</StyledLayerDescriptor>
```

## Nombres de las tablas creadas en postgis

* u2_departamentos
* u2_sitarq_anla_wgs84
* u2_dept_arq

## Nombres de las capas y estilos publicadas en geoserver.

__Capa-postgres__                                                      
* u2_departamentos                                       
* u2_sitarq_anla_wgs84                                 
* u2_dept_arq                                               

__Estilo__ 
* u2_estilo_departamento
* u2_estilo_sitarq_anla_wgs84
* u2_estilo_dept_arq

__Capa-GeoServer__
* Departamentos
* Hallazgos
* Departamento arqueologico

## Url de la previsualización del grupo de capas en Geoserver

Url: http://34.83.176.208:18080/geoserver/clase/wms?service=WMS&version=1.1.0&request=GetMap&layers=clase%3AArqueologico&bbox=-81.73579%2C-4.22788%2C-66.8473281860352%2C13.3948106765747&width=648&height=768&srs=EPSG%3A4326&format=application/openlayers

## Pantallazos con la forma en que los usuarios pueden consultar su geoservicio a través de QGIS

1. Creamos una conexion nueva al servicio:

![img2](IMAGENES/qgis_WMS1.jpg)

2. Definimos los detalles de la conexion con la Url suministrada abajo.

![img2](IMAGENES/qgis_WMS2.jpg)

3. Luego de establecer la conexion, seleccionamos para que se agregue al Qgis el servicio Hallazgos Arqueologicos Col

![img2](IMAGENES/qgis_WMS3.jpg)

WMS: http://34.83.176.208:18080/geoserver/clase/wms?service=WMS&version=1.1.0&request=GetMap&layers=clase%3AArqueologico&bbox=-81.73579%2C-4.22788%2C-66.8473281860352%2C13.3948106765747&width=648&height=768&srs=EPSG%3A4326&format=geojson

## Ventajas / desventajas / dificultades encontradas durante el proceso

En la capa inicial se tenia un total de 6599 registros, pero solo se logro subir 3000 registros
el error que salio fue el siguiente.

Ha ocurrido un error mientras se ejecutaba el código de Python:
db_manager.db_plugins.plugin.ConnectionError: server closed the connection unexpectedly This probably means the server terminated abnormally before or while processing the request.
Traceback (most recent call last):
File "C:/PROGRA1/QGIS31.4/apps/qgis-ltr/./python/plugins\db_manager\db_plugins\connector.py", line 86, in _execute
cursor.execute(sql)
psycopg2.OperationalError: server closed the connection unexpectedly
This probably means the server terminated abnormally
before or while processing the request.
During handling of the above exception, another exception occurred:
Traceback (most recent call last):
File "C:/PROGRA1/QGIS31.4/apps/qgis-ltr/./python/plugins\db_manager\dlg_import_vector.py", line 371, in accept
self.db.connector.createSpatialIndex((schema, table), geom)
File "C:/PROGRA1/QGIS31.4/apps/qgis-ltr/./python/plugins\db_manager\db_plugins\postgis\connector.py", line 970, in createSpatialIndex
self._execute_and_commit(sql)
File "C:/PROGRA1/QGIS31.4/apps/qgis-ltr/./python/plugins\db_manager\db_plugins\connector.py", line 100, in _execute_and_commit
self._execute(None, sql)
File "C:/PROGRA1/QGIS31.4/apps/qgis-ltr/./python/plugins\db_manager\db_plugins\connector.py", line 89, in _execute
raise ConnectionError(e)
db_manager.db_plugins.plugin.ConnectionError: server closed the connection unexpectedly
This probably means the server terminated abnormally
before or while processing the request.
Versión de Python: 3.7.0 (v3.7.0:1bf9cc5093, Jun 27 2018, 04:59:51) [MSC v.1914 64 bit (AMD64)]
Versión de QGIS: 3.4.13-Madeira Madeira, 64ad560274
Ruta de Python:
C:/PROGRA1/QGIS31.4/apps/qgis-ltr/./python
C:/Users/LENOVO/AppData/Roaming/QGIS/QGIS3\profiles\default/python
C:/Users/LENOVO/AppData/Roaming/QGIS/QGIS3\profiles\default/python/plugins
C:/PROGRA1/QGIS31.4/apps/qgis-ltr/./python/plugins
C:\Program Files\Hexagon\ERDAS IMAGINE 2015\usr\lib\Win32Release\python
C:\Program Files\QGIS 3.4\bin\python37.zip
C:\PROGRA1\QGIS31.4\apps\Python37\DLLs
C:\PROGRA1\QGIS31.4\apps\Python37\lib
C:\Program Files\QGIS 3.4\bin
C:\PROGRA1\QGIS31.4\apps\Python37
C:\PROGRA1\QGIS31.4\apps\Python37\lib\site-packages
C:\PROGRA1\QGIS31.4\apps\Python37\lib\site-packages\win32
C:\PROGRA1\QGIS31.4\apps\Python37\lib\site-packages\win32\lib
C:\PROGRA1\QGIS31.4\apps\Python37\lib\site-packages\Pythonwin
C:/Users/LENOVO/AppData/Roaming/QGIS/QGIS3\profiles\default/python
C:/Users/LENOVO/AppData/Roaming/QGIS/QGIS3\profiles\default/python/plugins\qgis2web
C:\Users\LENOVO\AppData\Roaming\QGIS\QGIS3\profiles\default\python\plugins

![img2](IMAGENES/Error_cargue_shp.JPG)

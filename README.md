# Fixes to common problems when migrating QGIS plugins from v2.18 to v3.x

Below is a list of fixes to common PyQGIS migration issues I collated whilst migrating a QGIS 2.18 plugin to QGIS 3.2 (and then to 3.4). I documented each error message and fix to make things easier if I came across the same issue again, and subsequently figured it may be of user to others who were migrating their plugins too.



## Table of contents
* [Error messages and fixes](#error-messages-and-fixes)
    * [Attribute errors](#attribute-errors)
    * [Name errors](#name-errors)
    * [Type errors](#type-errors)
* [See also](#see-also) (other hints/tips for plugin migration and associated troubleshooting)
* [Contributions](#contributions)
* [License](#license)



## Error messages and fixes

Details of error messages are documented as sub-headings, with fixes and examples for each listed underneath.  Where possible I've included links back to relevant documentation too.  

Error messages are listed alphabetically, and as such are organised by error type (`AttributeError`, `NameError`, `TypeError`).  
 
---
### Attribute errors:
#### AttributeError: type object 'Qgis' has no attribute 'Line'
#### AttributeError: type object 'Qgis' has no attribute 'WKBPoint'
#### AttributeError: type object 'Qgis' has no attribute 'WKBPolygon'

To fix: replace `Qgis.<Old Type>` with `QgsWkbTypes.<New Type>`

e.g.
```python
QGis.Line

# becomes
QgsWkbTypes.LineGeometry


Qgis.WKBPolygon 

# becomes
QgsWkbTypes.Polygon


Qgis.WKBPoint

# becomes
QgsWkbTypes.Point
```

See also: [QGIS Python API (PyQGIS) doc for qgis.core.QgsWkbTypes](https://qgis.org/pyqgis/3.4/core/Wkb/QgsWkbTypes.html)

and [QGIS breaking changes 'General changes' section](https://qgis.org/api/api_break.html#qgis_api_break_3_0_global)


---
#### AttributeError: type object 'QgsGeometry' has no attribute 'fromMultiPolyline'

To fix: replace `.fromMultiPolyline([qgs_point_list])` with `.fromMultiPolylineXY([qgs_pointXY_list])`

e.g.
```python
QgsGeometry.fromMultiPolyline(qgs_point_list)

# becomes
QgsGeometry.fromMultiPolylineXY(qgs_point_xy_list)
```

See also: [QGIS Python API (PyQGIS) doc for qgis.core.QgsGeometry.fromMultiPolylineXY()](https://qgis.org/pyqgis/3.4/core/Geometry/QgsGeometry.html#qgis.core.QgsGeometry.fromMultiPolylineXY)

and [QGIS breaking changes for QgsGeometry](https://qgis.org/api/api_break.html#qgis_api_break_3_0_QgsGeometry)


---
#### AttributeError: type object 'QgsMessageBar' has no attribute 'INFO'
#### AttributeError: type object 'QgsMessageBar' has no attribute 'WARNING'
#### AttributeError: type object 'QgsMessageBar' has no attribute 'CRITICAL'
#### AttributeError: type object 'QgsMessageBar' has no attribute 'SUCCESS'

To fix: replace `QgsMessageBar.<MessageLevel>` with `Qgis.<MessageLevel>`

e.g.
```python
QgsMessageBar.INFO

# becomes
Qgis.Info


QgsMessageBar.WARNING

# becomes
Qgis.Warning


QgsMessageBar.CRITICAL

# becomes
Qgis.Critical


QgsMessageBar.SUCCESS

# becomes
Qgis.Success
```

See also: [QGIS API doc for Qgis.MessageLevel](https://qgis.org/api/classQgis.html#a60c079f4d8b7c479498be3d42ec96257)

and [QGIS breaking changes for QgsMessageLevel](https://qgis.org/api/api_break.html#qgis_api_break_3_0_QgsMessageBar)

---
#### AttributeError: type object 'QgsSymbolLayerRegistry' has no attribute 'instance'

To fix: replace `QgsSymbolLayerRegistry.instance()` with `QgsApplication.symbolLayerRegistry()`

e.g.
```python
QgsSymbolLayerRegistry.instance()

# becomes
QgsApplication.symbolLayerRegistry()
```

See also: [QGIS Python API (PyQGIS) doc for qgis.core.QgsApplication.symbolLayerRegistry()](https://qgis.org/pyqgis/3.4/core/other/QgsApplication.html#qgis.core.QgsApplication.symbolLayerRegistry)

and [QGIS breaking changes for QgsSymbolLayerRegistry](https://qgis.org/api/api_break.html#qgis_api_break_3_0_QgsSymbolLayerRegistry)


---
#### AttributeError: 'QgisInterface' object has no attribute 'legendInterface'

To fix: reference the layer (by `id`) within the `layerTreeRoot`, and set visibility there.

e.g.
```python
iface.legendInterface().setLayerVisible(layer, False)

# becomes
QgsProject.instance().layerTreeRoot().findLayer(layer.id()).setItemVisibilityChecked(False)
```

See also: [QGIS Python API (PyQGIS) doc for qgis.core.QgsLayerTreeNode.setItemVisibilityChecked()](https://qgis.org/pyqgis/3.4/core/Layer/QgsLayerTreeNode.html#qgis.core.QgsLayerTreeNode.setItemVisibilityChecked)

and [QGIS breaking changes 'Removed Classes' section](https://qgis.org/api/api_break.html#qgis_api_break_3_0_removed_classes)

and [this GIS Stack Exchange answer](https://gis.stackexchange.com/a/272547/106634)


---
#### AttributeError: 'QgsLayerTree' object has no attribute 'selectedLayers'

To fix: replace `.legendInterface()` with `.layerTreeView()`

e.g.
```python
iface.legendInterface().selectedLayers()

# becomes
iface.layerTreeView().selectedLayers()
```

See also: [QGIS Python API (PyQGIS) doc for qgis.gui.QgsLayerTreeView.selectedLayers()](https://qgis.org/pyqgis/3.4/gui/Layer/QgsLayerTreeView.html#qgis.gui.QgsLayerTreeView.selectedLayers)

and [QGIS breaking changes for QgsGeometry](https://qgis.org/api/api_break.html#qgis_api_break_3_0_QgsGeometry)


---
#### AttributeError: type object 'QgsGeometry' has no attribute 'fromPoint'

To fix: replace `.fromPoint()` with `.fromPointXY()`

e.g.
```python
QgsGeometry.fromPoint(qgs_point)

# becomes
QgsGeometry.fromPointXY(qgs_point_xy)
```

See also: [QGIS Python API (PyQGIS) doc for qgis.core.QgsGeometry.fromPointXY()](https://qgis.org/pyqgis/3.4/core/Geometry/QgsGeometry.html#qgis.core.QgsGeometry.fromPointXY)

and [QGIS breaking changes for QgsGeometry](https://qgis.org/api/api_break.html#qgis_api_break_3_0_QgsGeometry)


---
#### AttributeError: 'QgsGeometry' object has no attribute 'exportToWkt'

To fix: replace `.exportToWkt()` with `.asWkt()`

See also: [QGIS Python API (PyQGIS) doc for qgis.core.QgsGeometry.asWkt()](https://qgis.org/pyqgis/3.4/core/Geometry/QgsGeometry.html#qgis.core.QgsGeometry.asWkt)

and [QGIS breaking changes for QgsGeometry](https://qgis.org/api/api_break.html#qgis_api_break_3_0_QgsGeometry)


---
#### AttributeError: 'QgsPalLayerSettings' object has no attribute 'readFromLayer'

To fix: refer to [this GIS Stack Exchange answer](https://gis.stackexchange.com/a/273268/106634)

and [QGIS breaking changes for QgsPalLayerSettings](https://qgis.org/api/api_break.html#qgis_api_break_3_0_QgsPalLayerSettings)


---
#### AttributeError: 'QgsVectorLayer' object has no attribute 'setDisplayField'

To fix: replace `.setDisplayField(html)` with `.setMapTipTemplate(html)`

e.g.
```python
layer.setDisplayField(html)

# becomes
layer.setMapTipTemplate(html)
```

See also: [QGIS Python API (PyQGIS) doc for qgis.core.QgsVectorLayer.setMapTipTemplate()](https://qgis.org/pyqgis/3.4/core/Vector/QgsVectorLayer.html?#qgis.core.QgsVectorLayer.setMapTipTemplate)

and [QGIS breaking changes for QgsVectorLayer](https://qgis.org/api/api_break.html#qgis_api_break_3_0_QgsVectorLayer)


---
#### AttributeError: 'QgsVectorLayer' object has no attribute 'setLayerTransparency'

To fix: replace `.setLayerTransparancy(percentage)` with `.setOpacity(percentage / 100)`

e.g.
```python
layer.setLayerTransparency(60)

# becomes
layer.setOpacity(0.6)
```

See also: [QGIS Python API (PyQGIS) doc for qgis.core.QgsVectorLayer.setOpacity()](https://qgis.org/pyqgis/3.4/core/Vector/QgsVectorLayer.html#qgis.core.QgsVectorLayer.setOpacity)

and [QGIS breaking changes for QgsVectorLayer](https://qgis.org/api/api_break.html#qgis_api_break_3_0_QgsVectorLayer)


---
#### AttributeError: 'QgsVectorLayer' object has no attribute 'rendererV2'

To fix: replace `.rendererV2().symbols()` with `.renderer().symbol()` and access the symbol directly

e.g. 
```python
symbols = layer.rendererV2().symbols()
symbol = symbols[0]

# becomes
symbol = layer.renderer().symbol()
```

See also: [QGIS Python API (PyQGIS) doc for qgis.core.QgsSingleSymbolRenderer.symbol()](https://qgis.org/pyqgis/3.4/core/Single/QgsSingleSymbolRenderer.html#qgis.core.QgsSingleSymbolRenderer.symbol)

and [QGIS Python API (PyQGIS) doc for qgis.core.QgsVectorLayer.setRenderer()](https://qgis.org/pyqgis/3.4/core/Vector/QgsVectorLayer.html#qgis.core.QgsVectorLayer.renderer)

and [QGIS breaking changes 'Renamed Methods' section](https://qgis.org/api/api_break.html#qgis_api_break_3_0_renamed_methods)


----
#### AttributeError: 'QgsVectorLayer' object has no attribute 'setRendererV2'

To fix: replace `.setRendererV2(renderer)` with `.setRenderer(renderer)`

e.g.
```python
layer.setRendererV2(renderer)

# becomes
layer.setRenderer(renderer)
```

See also: [QGIS Python API (PyQGIS) doc for qgis.core.QgsVectorLayer.setRenderer()](https://qgis.org/pyqgis/3.4/core/Vector/QgsVectorLayer.html#qgis.core.QgsVectorLayer.setRenderer)



---
### Name errors:
#### NameError: name 'QGis' is not defined

To fix: replace `QGis.xxx` with `Qgis.xxx` (note change in case from `QG` to `Qg`)

e.g.
```python
from qgis.core import QGis
QGis.xxx

# becomes 
from qgis.core import Qgis
Qgis.xxx
```

See also: [QGIS API doc for Qgis](https://qgis.org/api/classQgis.html)

and [QGIS breaking changes for Qgis](https://qgis.org/api/api_break.html#qgis_api_break_3_0_Qgis)



---
### Type errors:
#### TypeError: QgsCoordinateTransform(): arguments did not match any overloaded call:

```
  overload 1: too many arguments
  overload 2: not enough arguments
  overload 3: not enough arguments
  overload 4: not enough arguments
  overload 5: argument 1 has unexpected type 'QgsCoordinateReferenceSystem'
```
  
To fix: add `QgsProject.instance()` to `QgsCoordinateTransform()` call

e.g.
```python
xform = QgsCoordinateTransform(src_crs, dest_crs)


# becomes
xform = QgsCoordinateTransform(src_crs, dest_crs, QgsProject.instance())
```

See also: [QGIS Python API (PyQGIS) doc for qgis.core.QgsCoordinateTransform](https://qgis.org/pyqgis/3.4/core/Coordinate/QgsCoordinateTransform.html)

and [QGIS breaking changes for QgsCoordinateTransform](https://qgis.org/api/api_break.html#qgis_api_break_3_0_QgsCoordinateTransform)


---
#### TypeError: QgisInterface.newProject(): 'thePromptToSaveFlag' is not a valid keyword argument

To fix: replace `thePromptToSaveFlag` with `promptToSaveFlag`

e.g.
```python
iface.newProject(thePromptToSaveFlag=False)

# becomes
iface.newProject(promptToSaveFlag=False)
```

See also: [QGIS Python API (PyQGIS) doc for qgis.gui.QgisInterface.newProject()](https://qgis.org/pyqgis/3.4/gui/other/QgisInterface.html#qgis.gui.QgisInterface.newProject)


---
#### TypeError: QgsProject.read(): arguments did not match any overloaded call:

```
overload 1: argument 1 has unexpected type 'QFileInfo'
overload 2: too many arguments
```

To fix: remove `QFileInfo` and just pass string to `QgsProject.read()`

e.g.
```python
project.read(QFileInfo(project_file))

# becomes
project.read(project_file)
```

See also: [QGIS Python API (PyQGIS) doc for qgis.core.QgsProject.read()](https://qgis.org/pyqgis/3.4/core/Project/QgsProject.html?#qgis.core.QgsProject.read)

and [QGIS breaking changes for QgsProject](https://qgis.org/api/api_break.html#qgis_api_break_3_0_QgsProject)


---
#### TypeError: QgsRectangle(): arguments did not match any overloaded call:

```
  overload 1: too many arguments
  overload 2: argument 1 has unexpected type 'QgsPoint'
  overload 3: argument 1 has unexpected type 'QgsPoint'
  overload 4: argument 1 has unexpected type 'QgsPoint'
  overload 5: argument 1 has unexpected type 'QgsPoint'
```
  
To fix: replace instances of `QgsPoint` with `QgsPointXY`

e.g.
```python
QgsRectangle(QgsPoint(x_min, y_min), QgsPoint(x_max, y_max)))

# becomes
QgsRectangle(QgsPointXY(x_min, y_min), QgsPointXY(x_max, y_max)))
```

See also: [QGIS Python API (PyQGIS) doc for qgis.core.QgsRectangle](https://qgis.org/pyqgis/3.4/core/other/QgsRectangle.html)

and [QGIS breaking changes 'Renamed Classes' section](https://qgis.org/api/api_break.html#qgis_api_break_3_0_renamed_classes)


---
#### TypeError: QgsRubberBand.addPoint(): argument 1 has unexpected type 'QgsPoint'

To fix: replace instances of `QgsPoint` with `QgsPointXY`

e.g.
```python
QgsPoint(..., ...)

# becomes
QgsPointXY(..., ...)
```

See also: [QGIS Python API (PyQGIS) doc for qgis.gui.QgsRubberBand.addPoint()](https://qgis.org/pyqgis/3.4/gui/other/QgsRubberBand.html?#qgis.gui.QgsRubberBand.addPoint)

and [QGIS breaking changes 'Renamed Classes' section](https://qgis.org/api/api_break.html#qgis_api_break_3_0_renamed_classes)


## See also:
If the message you are getting when trying to migrate your plugin is not on this page, you can refer to:
- The QGIS API ['Backwards Incompatible Changes' (a.k.a. API Break)](https://qgis.org/api/api_break.html) page
- The QGIS ['Plugin migration to QGIS 3'](https://github.com/qgis/QGIS/wiki/Plugin-migration-to-QGIS-3) page
- GIS for Thought's useful walk-through on ['Updating A Plugin From QGIS 2 to QGIS 3'](https://gisforthought.com/updating-a-plugin-from-qgis-2-to-qgis-3/)
- Stack Exchange's essential ['Geographical Information Systems'](https://gis.stackexchange.com) Q&A site

I also found the [First Aid QGIS plugin](https://plugins.qgis.org/plugins/firstaid/) quite useful throughout the process, see:  
[First Aid's GitHub page](https://github.com/wonder-sk/qgis-first-aid-plugin) and their [blog post](https://www.lutraconsulting.co.uk/blog/2016/06/12/introducing-first-aid-plugin/) introducing it for more details.



## Contributions:

Contributions to improve this document are welcome, whether in the form or corrections, changes, additional entries, or anything else you see fit.   Please submit a pull request.



## License:
<a rel="license" href="http://creativecommons.org/licenses/by/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">common-qgis-2-to-3-plugin-fixes</span> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/4.0/">Creative Commons Attribution 4.0 International License</a>.

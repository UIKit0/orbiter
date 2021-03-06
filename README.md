orbiter
=======
An orbiting camera with mouse input bindings.  This is similar to 3d-view-controls, except it doesn't do mode switching and fancy property syntactic sugar, making it much lighter weight.

Default controls:

Button | Interaction
-------|------------
Left mouse | Rotate
Shift + left mouse *or* scroll horizontally | Roll
Right mouse | Pan
Middle mouse *or* scroll vertically | Zoom


# Example

```javascript
var createCamera = require('../orbiter')
var bunny = require('bunny')
var perspective = require('gl-mat4/perspective')
var createMesh = require('gl-simplicial-complex')

var canvas = document.createElement('canvas')
document.body.appendChild(canvas)
window.addEventListener('resize', require('canvas-fit')(canvas))

var gl = canvas.getContext('webgl')

var camera = createCamera(canvas, {
  eye:    [50,0,0],
  center: [0,0,0],
  zoomMax: 500
})

var mesh = createMesh(gl, {
  cells:      bunny.cells,
  positions:  bunny.positions,
  colormap:   'jet'
})

function render() {
  requestAnimationFrame(render)
  if(camera.tick()) {
    gl.viewport(0, 0, canvas.width, canvas.height)
    gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT)
    gl.enable(gl.DEPTH_TEST)
    mesh.draw({
      projection: perspective([], Math.PI/4, canvas.width/canvas.height, 0.01, 1000),
      view: camera.matrix
    })
  }
}
render()
```

# Interface

## Constructor

#### `var camera = require('orbiter')(element[, options])`
Creates a new camera object.

* `element` is a DOM node onto which the orbiter is attached
* `options` is an object with the following optional properties:
    + `eye` - the position of the camera in world coordinates (Default `[0,0,10]`)
    + `center` - the target of the camera in world coordinates (Default `[0,0,0]`)
    + `up` - the up vector of the camera (Default `[0,1,0]`)
    + `inputEnabled` if set to false, then disables input (Default `true`)
    + `delay` - amount to delay interactions by for interpolation in ms (Default `16`)
    + `rotateSpeed` - rotation scaling factor (Default `1`)
    + `zoomSpeed` - zoom scaling factor (Default `1`)
    + `translateSpeed` - translation/panning scale factor (Default `1`)
    + `flipX` - flip X axis for rotations (Default `false`)
    + `flipY` - flip Y axis for rotations (Default `false`)
    + `zoomMin` - minimum zoom distance (Default `0.01`)
    + `zoomMax` - maximum zoom distance (Default `Infinity`)

## Geometric properties

#### `camera.matrix`
A 4x4 matrix encoded as a length 16 array representing the homogeneous transformation from world coordinates to view (camera) coordinates.

#### `camera.eye`
The position of the camera in world coordinates

#### `camera.up`
A vector pointing up in world coordinates

#### `camera.center`
The target of the camera in world coordinates

#### `camera.distance`
Euclidean distance from `eye` to `center`

## Methods

#### `camera.tick()`
Updates the camera state.  Call this before each frame is rendered to compute the current state of the camera.

**Returns** `true` if the state of the camera has changed since the last call to `tick`

#### `camera.lookAt(center, eye, up)`
Sets the camera center/eye/up vector to look at a fixed target

* `center` is the new center/target for the camera
* `eye` is the position of the camera in world coordinates
* `up` is a vector pointing up

#### `camera.box(lo, hi)`
Targets the camera at a bounding box.  This is a short cut for skipping some of the boilerplate associated with lookAt

* `lo` is the lower bound on the box
* `hi` is the upper bound on the box

#### `camera.rotate(yaw, pitch, roll)`
Applies an incremental rotation to the camera

* `yaw` is the amount to rotate about the y-axis (in xz plane of camera)
* `pitch` is the amount to rotate about the x-axis (in yz plane of camera)
* `roll` is the amount to rotate about the forward axis (in xy plane of camera)

#### `camera.pan(dx, dy, dz)`
Applies a relative motion to the camera, moving in view coordinates

* `dx,dy,dz` are the components of the camera motion vector

#### `camera.translate(dx, dy, dz)`
Translates the camera in world coordinates

* `dx,dy,dz` are the components of the translation vector

## Tuning parameters

#### `camera.distanceLimits`
A 2D array representing the `[lo,hi]` bounds on the zoom distance.  Note that `0 < lo < hi`.

#### `camera.flipX`
A flag controlling whether the camera rotation is flipped along the x-axis

#### `camera.flipY`
A flag controlling whether the camera rotation is flipped along the y-axis

#### `camera.delay`
The amount of delay on the interpolation of the camera state in ms

#### `camera.rotateSpeed`
Camera rotation speed scaling factor

#### `camera.zoomSpeed`
Camera zoom speed scaling factor

#### `camera.translateSpeed`
Camera translation speed scaling factor

#### `camera.element`
The DOM element the camera is attached to

# License
(c) 2015 Mikola Lysenko. MIT License

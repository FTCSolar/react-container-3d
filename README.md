# react-container-3d
A container for three.js canvas renderer as a react component

![alt text](https://github.com/lucascassiano/react-container-3d/raw/master/docs/react-container3d.gif)

## Install
```javascript
npm install --save react-container-3d && three
```
## Live Demo
Try it on [CodeSandbox](https://codesandbox.io/s/0360pzv070):

[![Edit 0360pzv070](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/0360pzv070)

## Videos
Apps using this component
- [video 3](https://www.youtube.com/watch?v=gN7krypWtCc&t=113s)
- [video 2](https://vimeo.com/246214445)
- [video 1](https://vimeo.com/244749497)

## Usage
```javaScript
import * as THREE from 'three';
import Container3d from 'react-container3d';

...

<div className="canvas-3d">
<Container3d aspect={16 / 9} percentageWidth={"100%"}/>
</div>
```

# Container Drawing Methods
p5.js (and the original Processing, of course) was used as inspiration to define the life cycle names: setup and update;
```javascript

//called at when renderer is defined
Setup = (scene, camera, renderer) =>{
        console.log("starting 3d scene");
    }

//called every frame
Update = (scene, camera, renderer) =>{
        //console.log("scene", scene);
}
```
## Visual style Component Props
| prop name | prop type | description | 
|:---------:|:---------:|:-----------|
| width     |  Int      |  width in pixels  |
| height    |  Int      |  height in pixels  |
| percentageWidth| String or Int |  percentage related to screen width |  
| aspect  | float     |  width/height aspect ratio   |
| fitScreen | boolean |  if the component will stretch it self to fit the screen open area|
| marginLeft | Int | margin left in pixels |
| marginTop | Int | margin top in pixels |
| marginBottom | Int | margin bottom in pixels | 


## Execution Methods
| prop name | prop type | parameters| 
|:---------:|:---------:|:-----------|
| setup | function | scene, camera, renderer|
| update | function | scene, camera, renderer|
| onHoverStart | function | object, scene, camera, renderer|
| onHoverEnd | function | object, scene, camera, renderer|
| onError | function | error |

## 3D Scene elements Props
| prop name | prop type | description | default |
|:---------:|:---------:|:-----------|:-------:|      
| addControls| boolean | if OrbitControls will be added | true |
| addGrid | boolean | if the GridHelper will be added | true | 
| addLight | boolean | if default light will be added | true | 
| enableZoom | boolean | if the zoom from OrbitControls is enabled | true | 
| enableKeys | boolean | if the keys from OrbitControls are enabled | true|
| enablePan | boolean | if the user can pan the viewport with the mouse. Feature from OrbitControls | true |


## Default 3d elements
The grid, orbitControls and lights are by default added to the scene. 
they are also easily removed:
```JavaScript
<Container3d aspect={1} percentageWidth={"100%"} addGrid={false} addControls={false} addLight = {false}/>
```

enableZoom, enablePan and enableKeys (from orbit controls) are also defined as boolean props. 

## FitScreen mode
Sometimes we just want a 3D environment to add some models and have some interactivity. Due to the drag of attention to this environment, most of times, this component occupies the main portion of the screen, so we added this simple 'fitScreen' parameter, if this is on the component will try to adjust to fill the height and width of the screen. you can use the props marginTop, marginLeft, marginBottom, marginRight to set pixels of distance. (note: we tried with css, however the renderer update wasn't happening when the screen changed).

## Usage with react-cubeview
Both components were designed to work coupled - you can read more about react-cubeview [here](https://www.npmjs.com/package/react-cubeview). They were separated due to development simplification (We might, at some point, have enough 3D UI components to create a simple UI Kit... but that's for the future).
```jsx
import * as THREE from 'three';
import Container3d from 'react-container-3d';

//you need to install it using npm install --save react-cubeview
import CubeView from 'react-cubeview';
import 'react-cubeview/css/react-cubeview.css';

let object1;

class App extends Component {
    //will return the main container (canvas)
    getMainCanvas() {
        var mainCanvas = ReactDOM.findDOMNode(this.c3d);
        return mainCanvas;
    }
    //will update the camera angles/position from the orbitcontrols on the c3d
    updateAngles(phi, theta) {
        this.c3d.setAngles(phi, theta);
    }

    onHoverStart(object, scene, camera, renderer){
        console.log("hovering", object);
    }

    onHoverEnd(object, scene, camera, renderer){
        console.log("exiting", object);
    }
    //called when the scene is created
    Setup = (scene, camera, renderer) =>{
        //add cool 3d objects here ~ remember to import THREE
        var geometry = new THREE.SphereGeometry( 1 );
		var material = new THREE.MeshBasicMaterial( { color: 0x000000, wireframe: true } );
        object1 = new THREE.Mesh( geometry, material );
				scene.add( object1 );
    }

    //called every frame
    Update = (scene, camera, renderer) =>{
        //animate things
        object1.rotation.x = time * 0.0005;
		object1.rotation.y = time * 0.001;
    }

    render(){
        return(
            <div className = "App">
                
                <div className="canvas-3d" >
                    <Container3d marginTop={30} aspect={16 / 9} percentageWidth={"100%"} fitScreen ref={(c) => this.c3d = c} key={"c3d"} marginBottom={110} update={this.Update} setup={this.Setup} onHoverStart={this.onHoverStart} onHoverEnd={this.onHoverEnd}/>
                </div>

                <div className="cube-view">
                    <CubeView aspect={1} hoverColor={0x0088FF} cubeSize={2} zoom={6} antialias={false} onUpdateAngles={this.updateAngles} relatedCanvas={this.getMainCanvas} />
                </div>

            </div>
        );
    }

}
```
## Custom Size/Background (CSS)
If wrapped around a div component, you can play easily with the canvas size;

By default, the canvas has a transparent background, this means your background color must be added using css background. We wanted gradient backgrounds.

```jsx
    <div className="canvas-3d">
        <Container3d ... >
    </div>
```

```css
.canvas-3d{
    margin-top:30px;
    width:100%;
    min-width: 500px;
    background: linear-gradient(to bottom,  rgba(51,51,51,0) 0%,rgba(80,80,80,1) 100%);
  }
```

## Loading 3D Models
You can use pretty much all three.js model loaders, however be awere if you're using ES6, make sure all of them are wrapped as ES6 classes or ES5 modules.
Also, for .obj we tested the [three-react-obj-loader](https://www.npmjs.com/package/three-react-obj-loader) combined with [three-react-mtl-loader](https://www.npmjs.com/package/three-react-mtl-loader).

## Storybook

clone or download the git page and run:
```
npm install 
npm start
```
check http://localhost:6006
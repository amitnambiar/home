# Quick Start

O-LAP follows a decentralized way of running things. The designs are logic written by the designer in Javascript which is displayed to the user by fetching the code from the designer's repository and running it in the user's browser. The designs and data themselves are maintained by each Github repository holder (and Github of course).  
Anyone can make a design by cloning the `starter_project` repository, and modifying it to make their own design. Once the design is ready to be published, you can submit the link to your repository to the O-LAP app as a pull request.  
Community admins moderate this and once accepted, the link to the design repo is added to the O-LAP gallery. When a user visits the gallery all submitted designs are displayed. When the user opens a design, the framework directly fetches the design from the last commit on the master branch of the design's repository.  
We don't try to curate what/how your design looks or works like. However if we run into issues with unwanted content or abusing the platform we may have to change this.  

Get the starter project by cloning `https://github.com/O-LAP/starter_project.git`.  
The template project contains all files necessary to get going.
Once you clone the repository you will get the contents as follows:  
```  
- design
  - Design.js
  - EmptyDesignTemplate.js
  - designs.jpg
- olap
  - css
    - materialize.min.css
    - style.css
  - js
    - materialize.js
      - init.js
      - jquery-3.3.1.min.js
      - materialize.min.js
    - three.js
      -	Detector.js
	  - mode.js
	  - OBJExporter.js
	  - OBJLoader.js
	  - OrbitControls.js
	  - three.js
	  - threeScene.js 
	  - THREEx.FullScreen.js
	  - THREEx.WindowResize.js
	  - THREEx.screenshot.js
	  - verb.js
	  - verbToThreeConversion.js
    - OLAPFramework.js
- dev.html
- README.md
```  

The folder named `olap` contains files for the framework and is meant to be kept as it is.  
The `designs` folder contains the files meant for the designer to work with.  
&nbsp;&nbsp;&nbsp;&nbsp; The `Design.js` file contains some sample code showing a cube which can parametrically modified.  
&nbsp;&nbsp;&nbsp;&nbsp; The `EmptyDesignTemplate.js` file is a blank canvas that you can use to start your design. (Replacing the `Design.js` file).   
The `README.md` is meant to be the read me file for the design and git repo.  
The `dev.html` file is the development harness which emulates the OLAP web app. (This file would have to be manually copied on updates.)  

The `starter_project` has files in place to let you run and test your design in a development environment and once you push it and register it with the main app, it runs smoothly with the framework as well. The framework requires the design logic to be captured in a Javascript object called `Design`. This object has a bunch of methods and properties which makes it play well with the O-LAP framework. They are as follows:  
```  
Design.info = { ... };
Design.inputs = { ... };
Design.inputState = { ... };
Design.init = function() { ... };
Design.onParamChange = function(params) { ... };
Design.updateGeom = function(group, sliceManager) { ... };
```  

You can add more methods/properties as you need without altering these.  

You can open up the `dev.html` file in a browser to see what the design looks like.  
It should show a simple cube which can be controlled using parameters in the browser.  
You can use your mouse to rotate (right click to pan) and view the design and play with the parameters to explore this 'design'.  
Let's quickly run through the javascript design file for the cube to understand how it works and then we can move on to creating our own design from scratch.  

At the top you will see the design meta information which looks like this.  
```  

Design.info = {
	"name": "Boxy",
	"designer": "Roxy",
	"version": "1.0.0",
	"license": "MIT",
	"short_desc": "Template design file demoing project setup.",
	"long_desc": "",
	"url": null,
	"tags": [ "", "" ]
}
```  

This is used to render information about the design in the gallery and wherever else the relevant information is required. Let's go thorugh each one.  
`name`: Used as the design display name.  
`designer`: Used as the name to be displayed as designer. The design is owned by the designer's repository under his Github account, but this name is used just for display.  
`version`: Like software, the code that generatesyo9ur design can changer overtime. You can use the version number to capture that as your design moves forward.  
`license`: Specify what license type you wish to apply for your designs.  
`short_desc`: Used to show short descriptions of your design wherever needed. Please keep it less than 140 characters.  
`long_desc`: Used to give a more detailed description for your design. Please keep it less than 2000 characters.  
`url`: Used to point to any external link you can maintain for your design.  
`tags`: You can add upto 10 tags for your design.  
`message`: Displayed in the UI to explain anything you may want about the parameters. *******************************  

Let's also quickly glance where these show up the user-interface.
![UI](https://raw.githubusercontent.com/O-LAP/home/master/imgs/ui-explain.jpg)

Below this is the section which has the logic for how the code works.  
It starts by declaring some variables that will be useful.  

Let us now look at the design object properties and methods described earlier.
```  

Design.inputs = { ... };
```  

This property is used to sepcify the parameters the designer would like to expose to a user via the user-interface. The UI is only updated once when the design is loaded.  
There are 3 types of paramaters you can provide - `slider`, `bool` and `select`.  
&nbsp;&nbsp;&nbsp;&nbsp; `slider` is used to allow the user to pick a numercial value from a continuous range. The values are in integers.  
&nbsp;&nbsp;&nbsp;&nbsp; `bool` allows the user to pick from a yes/no value.  
&nbsp;&nbsp;&nbsp;&nbsp; `select` allows the user to select one from a list of values.  

To add a parameters to your design you need to register it by adding a key-value pair to `Design.input`.
The key will be used to refer to the current value of the parameter and and the value is the configuration object for the parameter.  
```  

Design.inputs = {
	"width": { 
		"type": "slider",								// specify type
		"label": "Width",								// Label name is the name displayed for the property in the UI
		"default": 150,									// The value this parameter will be set on initializiation
		"min": 100,										// The upper range of the slider
		"max": 200										// The lower range of the slider
	},
	"height": { 
		"type": "slider",						
		"label": "Height",
		"default": 150,
		"min": 100,
		"max": 200
	},
	"doubleWidth": {
		"type": "bool",
		"label": "Double Width",
		"default": false
	},
	"colour": {
		"type": "select",
		"label": "Colour",
		"default": COL_RED,
		"choices": [COL_RED, COL_GREEN, COL_BLUE]		// list of choices to be offered
	},
	"finish": {
		"type": "select",
		"label": "Finish",
		"default": FIN_MATT,
		"choices": [FIN_MATT, FIN_GLOSS]
	}
}
```  

Below this you will find the input state property.  
You can use this to access the current value set by the user for the parameters at all times. For eg.  
To access the value for `height` parameter you can use `Design.inputState.height`.  
```  

Design.inputState = { ... };
```  

The initializiation method is run once at start; and can be used this to initialize any values. It is called before loading the UI. The render calls or parameter update calls are made after the UI is updated.  
```  

Design.init = function() { ... };
```  

The parameter change callback is called whenever the user changes a parameter via the user-interface. It is called as soon as the value is changed, so it will be called each time the value updates when a user drags the slider.  
```  

Design.onParamChange = function(params) { ... };
```  

The call to update the geometry is made when the design is required to be updated in the view. It happens after every parameter change and on initital load. It passes in an empty `THREE.Object3D` which is the container for the designer to add geometries to and a `SliceManager` which the designer can use to specify how to make the 'slices' for the design. References from the previous update call are discarded and fresh instances for every call are used.  
```  

Design.updateGeom = function(group, sliceManager) { ... };
```  

Let us quickly look at what the `updateGeometry` method looks like for this simple cube.
```  

Design.updateGeom = function(group, sliceManager) {
	var geometry = new THREE.BoxGeometry( 200, Design.inputState.height, Design.inputState.width * ((Design.inputState.doubleWidth) ? 2 : 1) );
	var material = getMaterial(Design.inputState.colour, Design.inputState.finish);
	var cube = new THREE.Mesh( geometry, material );
	cube.position.y = Design.inputState.height/2;

	sliceManager.addSliceSet({uDir: true, start: -80, end: 80, cuts: 3});
	sliceManager.addSliceSet({uDir: false, start: -90, end: 90, cuts: 4});

	group.add( cube );
}
```  

The first 4 lines are pure threeJS code. On the first line it creates a `BoxGeometry` using values from the current state of inputs for `height` and `width`. The `doubleWidth` boolean doubles the width if set to true.  
The next line creates a new material based on the `colour` and `finish` values. The next line create a threeJS mesh.  
The slicing specifications are specified in the next two lines. Make sure to specify the slicing configuration before adding the geometry.  
The last line adds it to the group which is added to the scene by the framework after the end of this call.  

The `sliceManager` is a special object passed in, to allow the designer to specify how the design is to be 'sliced'.
Slicing is the process of converting the solid design into these waffle strcutures.
![U](!https://raw.githubusercontent.com/O-LAP/home/master/imgs/u.gif)
![V](!https://raw.githubusercontent.com/O-LAP/home/master/imgs/v.gif)
![Cross](!https://raw.githubusercontent.com/O-LAP/home/master/imgs/cross.gif)
![Slot](!https://raw.githubusercontent.com/O-LAP/home/master/imgs/slot.gif)
You slice the volume in two perpendicular directions with half length cutouts in each of them to allow the other piece to slide in.  
You can use the `addSliceSet(config)` method to add slicers for the design. The cuts are made vertically and all designs are expected to have atleast 2 slice sets which are perpedicular to each other. The main consideration while determining the slices should be weight distribution and strength as these are load-bearing elements of the furniture design when fabricated.
The configuration object must carry the following information for slicing.
```
	uDir: true/false		// direction of the cut, aligned to the world XY axis
	start: number			// the start coordinate position in the axis specified
	end: number				// the end coordinate position in the axis specified
	cuts: number			// number of cuts to be made along the axis (minimum 2)
```

Please be extra careful about this part. While the designs are easy to play with using computer modelling, the idea is they will be eventually made for use using physical materials.  

ThreeJS library is used to render all geometry and you can put anything that is compatible in a `THREE.Object3d` object into group. Since the designs have to be cut and fabricated you would need to make sure that the designs are geometries which are suitable for such a process.
The slicers are also procedurally added so you can change the slicing behaviour as the design changes. And you can have more than 2 of them.  
The cuts are made vertically and the grooves used for interlocking are represented as single lines and distributed in exact halves for each cut direction.
This should allow interlocking for all ground touching elements. Parts which don't touch the ground need more thought and the slices need to conform to certain rules to make sure they can be fabricated.
Refer the (design guidelines)[https://github.com/O-LAP/home/blob/master/guidelines.md] for more information about slicing.

That's all one needs to know to get going.
You can experiment playing with the cube and downloading the output. The output file contains the CAD design that can be used for farication with a CNC machine.  


## Submit Your Design
Designs will be accepted into the main repo via pull requests. This will allow for a meaningful discussion in the add publish process.
We are working on the instructions for it. Coming soon.

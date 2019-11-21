# three-system

A minimal library that implements the entity component system pattern on top of the three.js event system

## Usage

The library exports a single function used to extend three.js objects that inherit from EventDispatcher. This includes everything concerning the scenegraph. A central object should be used as the container and usually that would be the root scene object.

```js

    import attachSystem from "three-system";

    var systemDefinition = {

        // ev will be the original event that triggers the corresponding function handler 
        
        // objects contains all entities with this system and are managed by three-system 
        
        // name will be the same that was passed to attachSystem for symbol agnostic systems
        
        init: function (ev, objects, name) {
            
            //for init and remove, ev must always contain a property "entity" with an object3D

            // the returned data will be stored in entity.userData[name]
            return {...}

        },

        remove: function(ev, objects, name) {
        
            // here we can access ev.entity.userData[name] to do resource cleanup
        
            // the objects array and entity.userData are managed by three-system
        
        },

        // custom handler, we will have to dispatch it on the container object ourselves
        
        tick: function(ev, objects, name) {
            
            // process our objects in batch, objects is a list of object3D entities

            objects.forEach( function( entity ) {
            
                // the data/component for each entity is at entity.userData[name]
            
            });

        }

    }

    // we setup the scene with the system
    attachSystem(scene, "mySystem", systemDefinition)

    var object = new THREE.Group();

    // the following triggers init, if entity already had the component,
    // remove will be called for the previous set of component data
    
    scene.dispatchEvent( { type: "mySystem/register", entity: object });

    // this will call the "tick" handler for all scene systems that defined it
    // we can pass any data we want in the event object, they'll be preserved
    scene.dispatchEvent( { type: "tick", ... });

    // finally we detach the object from the system
    
    scene.dispatchEvent( { type: "mySystem/unregister", entity: object });

```
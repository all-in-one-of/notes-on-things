# Questions

## How to... 

### How to Volumes... 

#### How to visualize a scalar volume field. 
* Add a volume visualization node. 
* Play around with ramp settings and density scale to taste.

#### How to visualize a vector volume field. 
* Add a bound node with input from volume 
* Add a points from volume with input from bound node. 
* Add a volume trail node with first input the points from volume node and second input as the original volume. 

------------

## Why?

### Why Volumes?

#### Why do we prefer VDB SDFs to regular Volume SDFs?
* They are less error prone and faster to generate.

------------

## How?

### How Volumes?

#### How are Volume SDFs stored?
* Each voxel center contains the radius of the circle that touches the field.
* The inside of the surface gets a negative prefix, the outside gets a positive prefix.
* The surface itself is represented by the sign change passing through 0.

------------

## Houdini Links

- [John Svensson's VEX Snippets](http://www.johnsvensson.se/vex-houdini/)

- [Jtomori's VEX Snippets](https://github.com/jtomori/vex_tutorial/blob/master/README.md)

- [FlexMonkey's VEX and SWIFT Tutorials](http://flexmonkey.blogspot.com/)

### Houdini Tools

- [Sublime Text Editor add-on for VEX](https://github.com/teared/VEX)

### HDK

- [Official HDK](https://www.sidefx.com/docs/hdk/examples.html)

- [Deborah Fowler's HDK Resources](http://www.deborahrfowler.com/C++Resources/HDK-Resources.html)

- [Creating a simple OpenVDB Sop](https://jurajtomori.wordpress.com/2017/12/01/creating-simple-c-openvdb-node-in-hdkcreating-simple-c-openvdb-node-in-hdk/)


------------

# Entagma Tutorials

## [Volumes 101: Volume Basics](https://www.patreon.com/posts/volumes-101-13902771)

### Volume SOPS
* Drop down a geo node and enter into it, delete the default node.

#### Volume SOP ( for creating Volumes )
* Density, v or vel for velocity are common volume names.
* Uniform sampling by size allows you to set the size of each voxel. 0.05 is a good value.
* Middle mouse over node will tell you the dimensions and number of voxels.
* Volume VOPs allow you to manipulate individual voxel elements.

#### Volume VOP SOP ( for manipulating Volumes )
* Input Parameters:
   - p = current position of voxel
   * density is scalar value
   * ix, iy, iz are 0-based indices of current voxel
   * resx, resy, rez are number of voxels in each dimension
   * center will return the volume center
   * orig will return the bottom left corner of volume
   * size will return the size of volume in world-space as a vector
   * dPdx, dPdy, dPdz are vector steps you have to take to get to next voxel
   * BB will return current voxel position within the bounding box (0-1)
* Unified Noise
   * A bit slower than anti-aliased noise, but offers better parameters to tweak the noise.
   * Wire in the current position to unified noise -> position, and make sure set to 3d input and 1d output. Wire density into volume output.
   * To tweak the parameters one level up: middle-click and promote the basis, freq, offset, period, fractal, octave, lacunarity and roughness.
* Ramp Parameter
   * To tweak noise's contrast one level up: drop down a Ramp Parameter SOP wired between the Unified Noise and the output. Make sure to set it as a spline ramp.
   * Can invert the outputs by dragging left handle past the right handle.

#### Volume Slice SOP ( for visualizing Volumes )
* Gives you a 2d color coded slice of a volume.
* Quick for visualize huge volumes, but clumsy because you can only see a 1d slice.

#### Volume Visualization SOP ( for visualizing Volumes )
* Crank up density slider to see values more clearly.
* Works on the entire volume.
* Bug: Volume Visualization sometimes gets "stuck", so you have to change the density scale by a tiny amount to see updated data.
* Has lots of cool presets to view different aspects of the data.
   * Increasing - Normal 0-1 ramp.
   * Decreasing - Inverted 1-0 ramp.
   * Hill - See middle values.
   * Valley - See everything but the middle values.
   * Step - Very steep cutoff in value display.
   * Square - Extreme values like 0 or 1 are not displayed.
* Emissions tab -> Emissions slider can set the glow from the volume. Make sure to specify the emission field as density (or whatever) to actually have the glow applied to only areas of density instead of the entire volume.

#### Primitive Node SOP ( for visualizing Volumes )
* Check the checkbox in Volume tab -> Adjust visualization. Then you can adjust the density slider just like in the volume visualization SOP.
* Display Mode
   * Smoke - the default. Use the density slider to adjust contrast of volume.
   * Rainbow - Display volume using colors mapped to voxel's position within the volume.
   * Isosurface - Display the volume as a solid surface. Need to adjust the display isocontour which tells Houdini to display voxels where that value is reached as a solid (SDF).

------------

## [Volumes 101: Vector Volumes](https://www.patreon.com/posts/volumes-101-13979215)

### Volume SOPS
* Drop down a geo node and enter into it, delete the default node.

#### Volume SOP ( for creating vector volumes )
* Make sure to set 'Uniform Sampling' to 'Size' and 0.01.
* Set rank to be Vector
* Set the name to 'v' for velocity.

#### Volume VOP SOP
* Curlnoise operator
   - Needs a positional input, so wire p to pos.
   * Expose freq, offset and type by middle clicking and selecting 'promote'.
* Bind Export operator
   - Exporting to density won't work, so we need a bind export operator.
   * Set type to vector and name to v, then hook up the curlnoise output.
   * Can delete the default 'density' output since its not used.
* Middle clicking shows that houdini creates three volumes: v.x, v.y and v.z

#### Volume Slice SOP ( for visualizing volumes )
* Need to set attribute to 'v'
* Still, kind of hard to see what's going on.

#### Volume Trail SOP
* Wire the volume into the second input.
* Needs a point field input in the first input.
* This can be useful for visualization or even modifying geometry.

#### Bound SOP
* Wire up the volume into it.
* Returns the bounds of the volume.

#### Points From Volume SOP
* Wire up bounds into it.
* Set the density of the points by using 'point separation' setting.
* Finally, wire it to the volume trail's first input.

------------

## [Volumes 101: Signed Distance Fields](https://www.patreon.com/posts/volumes-101-14120738)

### SDF Concepts
* Each voxel center contains the radius of the circle that touches the field.
* The inside of the surface gets a negative prefix, the outside gets a positive prefix.
* The surface itself is represented by the sign change passing through 0.

### Volume SOPS
* Drop down some test geometry, the rubber toy node.

### Iso Offset SOP
* Wire it up and change the output type from fog volume to SDF volume.
* Change uniform sampling to 'by size' and set value to 0.03.
* Error prone and takes a long time, so use VDBs instead.

### Volume Blur SOP
* Make sure to check 'use voxel radius' when using SDFs.

### Volume Slice SOP
* Slide the plane through to look into the volume and detect weird geometry.

------------

## [Volumes 101: VDBs vs. Standard Volumes](https://www.patreon.com/posts/volumes-101-vdbs-14233083)
- VDBs only store values along the surface, omitting the interior and exterior nodes.
- They are also multithreaded and open sourced.

### VDB From Polygons SOP
- Converts polygons to VDB SDF or density field.
- Much faster than Volumes and without artifacts.
- Exterior/Interior band Voxel settings are the number of voxels existing from the surface (default is 3 for each).
- Checking "Fill Interior" will fill the entire interior with voxels.
- If "Fill Interior" not checked, Houdini assumes interior has value of 1 and exterior has value of 0.

### VDB Smooth SDF SOP
- Different than the VDB Smooth SOP in that it is specifically for smoothing SDFs.
- Can pick different smoothing kernels: Average, Gaussain, etc.

### Adding Attributes
- Uncheck "Add Shader" in the test geo.
- Wire up a Color SOP.
- Make sure class is "Point"
- Pick color type "Random"
- Wire up a VDB From Polygons.
- Click on the plus icon next to Surface Attributes.
- Select Attribute as "point.Cd"
- Set the VDB Name for attribute as "color"
- Middle mouse over node and see two volumes: one float-volume called "surface" and one vector volume called "color.
- NOTE: VDBs allow you to store a vectore as a single volume, whereas Houdini Volumes would require three volumes.

### Booleans
- Can get better and faster results than normal Houdini Volumes.
- Add a second mesh: the pig-head.
- Wire up a VDB Combine SOP
- Pick operation as one of the SDF operations: SDF Union, SDF Intersection, SDF Difference.
- Drop down a Smooth Surface SDF SOP.

### Exterior/Interior Band Voxels
- Wire up a VDB Visualize Tree SOP
- Check the "Active Voxels" checkbox.
- Wire up a Clip SOP.
- Set to the X-Axis with { 1, 0, 0 } in Direction.
- Red dots are exterior band voxels, white dots are interior band voxels.
- Standard smooth could smooth into areas with no voxel data producing errors. The Smooth SDF instead activates voxels in those holes so we don't get errors.

### VDB Activate SDF SOP
- The Half-Width is the width around the surface in voxels. Half-Width of exterior band, the other Half Width of interior band.
- VDB Visualize Tree with Active Voxels -> Clip is a good way to see what's going on.
- Can also just check Fill-Interior in original VDB From Polygons to fill with active voxels. Also has an exterior band voxels option which does the same thing as Activate SDF -> half width.

### When to use VDB vs Volumes
- Use VDBs when fields are sparse.
- Use Volumes if you don't want to have to worry about activating voxels, or if the field isn't sparse.
- Use Volumes when you want to use OpenCL.
- Most standard building operations are quicker and less error-prone in VDBs.

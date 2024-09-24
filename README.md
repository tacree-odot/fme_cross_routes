# Building Cross Routes in FME
This project is to demonstrate some approaches to using the fme intersector transformer to build cross routes related
to intersections.  

## Assumptions
For purposes of this demonstration, the following assumptions about the data are made:

- The data provided is linear referenced line segments containing street name attributes.  We can think of this data as being a street name feature class from Roads and Highways or some other external resource.
- Since it 
- is linear referenced, we can assume the following:
  * The geometry contains the x, y vertices as well as measures
  * Each record contains a route identifier
  * Each record contains a street name
- The segmentation of the data is arbitrary, meaning that breaks in the geometry can be anywhere

## Goals
- The output shall contain segments that are continuous between each intersection
- Each intersection shall contain 1 cross route record for each route crossing the intersection
- Each related cross route shall be a point, and the measure shall be applied at the point of intersection
- The cross routes for each intersection shall be related by a shared intersection id.
- Terminal nodes are not considered intersections and are eliminated if they don't end at another route.
- If the route is the same but the street names are different, it shall be treated as an intersection.

## Overall Approach
1. Dissolve all the segmentation for each route/street traversal so the lines are continuous
2. Use the intersector transformer to break the continuous geometries where they intersect.  This provides links and nodes as outputs
3. Assign an id for each link between each intersection
4. Create nodes from the linked segment end points
5. relate the intersecting nodes to the segment nodes

## Interesting scenarios
- P-route/lollipop route example: 
  - TDELTR00668**C
  - PDELTR80472**C
- Line combining closed routes (ring geometries) that end up being multipart paths.  See the multipart_segments inspector
  - PDELMR81104**C 
- Discontinuous Routes with duplicate measures at different locations.  The start of the gap is the same measure as that of the end of the gap
  - TDELTR01819**C
  - TDELTR00129**C
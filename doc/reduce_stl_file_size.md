### Polygon reduction with MeshLab

If the meshes used in the urdf is very densed (more than 2 MB) then it may cause problems like reduction in fps or lagging when Rviz or Gazebo is opened. In order to avoid that you must decrease the stl file size in between 10 KB to 1.5 MB. There won’t be much difference in the visual. 
https://support.shapeways.com/hc/en-us/articles/360022742294-Polygon-reduction-with-MeshLab
In the MeshLab, select **Filters > Remeshing, simplification and construction > Quadratic Edge Collapse Detection**.
Here are the optimal option settings:

* Target number of faces – Keep it such that it is low as well as visual is not much affected (Do trial and error)
* Quality threshold: 1
* Preserve Boundary of the Mesh: Yes
* Preserve Normal: Yes
* Optimal position of simplified vertices: Yes
* Planar simplification: Yes

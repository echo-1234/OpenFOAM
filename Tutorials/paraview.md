## Two ways for post-processing
1. convert to VTK
  In the case main directory
  `foam to VTK`
  `paraview &`
2. use _<case_name>.foam_ 
  In the main case directory, create empty text file case.foam:
  `nano case.foam` and save the file
  `paraview case.foam &`
  
  
- cutting surface
_Filters_ -> _Slice_ filter in _Common_or_Recent_ submenu

- contours
  _Filters_ -> _Common_ -> _Contour_
  With the _Slice_ module highlighted in the Pipeline Browser, the user should select the     Contour filter.

- vector plots
before this, could delete or "eye-off" the previously created items
1. Generate a vector glyph for velocity at the center of each cell
   _Filters_ ->  _Alphabetical_ -> _Cell Centers_ -> Apply
2. create glyph
   with "centers" highlighted
   _Filters_ -> _Common_ -> _Glyph_
   setup the parameters

- streamline plot
1. _Filters_ ->  _Stream Tracer_
  The _Seed_ points should be specified along a _High Resolution Line Source_ running vertically through the centre of the geometry
2. _Filters_ -> _Tube_

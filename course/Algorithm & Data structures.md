
# Arrays

https://frontendmasters.com/courses/algorithms/arrays-data-structure/
- array is not []
- array is collection of elements stored at contiguous memory locations
- the reason array need to be specified in size is that it do not need to reallocate
- insertion = overwrite
- insertion: go to the address, plus the width of the type * offset 
- deletion: go to the offset, set zero value
- Big O Time Complexity: 
	- O(1) because it all is an instant operation, it do not need to go through all the array to access a specified element

https://frontendmasters.com/courses/algorithms/arrays-q-a/
- array can't be grow
- array can be reallocate: create a new array, put old values in
-  optimize: you need to know how big for your underlying buffer to create, so that u can use it effectively without reallocate too much 

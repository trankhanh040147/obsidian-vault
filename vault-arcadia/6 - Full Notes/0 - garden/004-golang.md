# How to Increase Slice Capacity in Go
https://bytegoblin.io/blog/how-does-the-capacity-change-when-you-append-to-a-slice-in-go

## What Happens When `len == cap`?

When you attempt to add an element to a slice when its length is equal to its capacity (`len == cap`), Go performs a process known as **reallocation**. This involves the following steps:

1. **Create a New, Larger Underlying Array:** When a new element is appended, Go allocates a new array that is typically double the original capacity.
    
2. **Copy Existing Elements:** It then copies the content of the old array to the new array.
    
3. **Append the New Element:** The new element is added to the new underlying array.
    
4. **Update Slice Metadata:** Finally, the metadata of the slice, such as the pointer to the array, length, and capacity, are updated.

--- 


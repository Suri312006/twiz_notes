# General

### Memory
- [MMU](https://en.wikipedia.org/wiki/Memory_management_unit) (Memory Management Unit)
    - maps memory from virtual to physical
    - When a program refers to a location that's not in physical memory, 
        it will cause an interrupt that the kernel deals with
        - Twizzler uses this interrupt during the first access of an object to
            verify that the program trying to access the memory region has the 
            capability to do so.
- [MMAP](https://man7.org/linux/man-pages/man2/mmap.2.html)
    - Creates a new mapping in the virtual address space of calling process. 
    - Basically a way to map more memory into the current process


- Virtual Address Spaces
    - acknowledge that virtual address spaces are incorrect for this topic but 
    they leverage the existing hardware that is built for this


# Twiz specific

- Objects
    - maybe persistent data that is identified by unique 128 bit object ID
        - used to provide a contiguous reigion of memory for semantically 
        related data
    - mapped on demand
    - sets access policy by programming the MMU
        - what does program the MMU even mean?
    - can expand objects to be accessible across systems

    - created by the `create` syscall
        - can take in a existing object id, in which twiz will use COW
    - deleted using the `delete` syscall
        - objects are ref coutned so once reference count reaches Zero, it can 
        be deleted

- FOT
    - exists inside each object
    - allows increased ID space without increasing pointer size
    - i think it allows for daisy chaining / russian dolling of objects
    - each entry flags have rwx permissions, that are requests which will be 
    enforced by access control
    - names are also allowed in FOT entries
        - they say it enables late binding but im not really sure what that means
        - names are passed onto a resolving function, allowing more flexibility 
        than unix paths



- Views
    - enable a program to map objects for access
    - laid out like a page table
    - each entry contains an object ID, and rwx permission bits
    - upon page-fault, fault handler tries to handle by 
        - copy on write (unsure what this means)
        - checking permissions (mentioned earlier)
        - maps in an object
        - if it cant handle, raises exception to user-space
    - have 2 Syscalls
        - `set_view`: allows a thread to change to a new view
            - can let a thread execute a new program
            - jump across programs to i.e. accomplish protected task
        - `invalidate_view`: lets a thread inform kernel of changed / deleted entries
            

- Security Contexts

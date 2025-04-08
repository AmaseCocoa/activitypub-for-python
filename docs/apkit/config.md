# Config Class

The `Config` class is responsible for managing the configuration settings of the application. It provides various attributes and methods to customize the behavior of the framework.

## Attributes

- **allow_private_ip**: `bool`  
  Indicates whether private IP addresses are allowed. Default is `False`.

- **max_redirects**: `int`  
  Specifies the maximum number of redirects allowed. Default is `5`.

- **kv**: `BaseStore`  
  Represents the key-value store used. Default is an instance of `InMemoryStore`.

- **inbox_urls**: `list[str]`  
  A list of URLs for the inbox. Default is `["/inbox"]`.

- **logger**: `Logger`  
  Logger instance for the `apkit` framework, configured with the name "apkit". This logger typically does not need to be changed.

- **use_apkit_router**: `bool`  
  Indicates whether to use the `apkit` router. Default is `True`.

!!! note  

    **use_apkit_router**: This attribute enables a simple router that returns `True` if all segments of a path (split by `/`) match.
    
    If disabled, the paths set in `APKit.add_actor_endpoint` and `APKit.actor` will not be utilized. However, disabling this option may improve performance when using longer paths for actors due to its inherent nature.

# config_maps

* ConfigMaps should be managed via manifests and tightly couple to the PODs they are being deployed with. Values for the configs maps can be swapped in (token replaced) at build time from a central storage repository.
* Configurations should be provided/consumed in the following priority order
     1. The deploy application consumes its configurations directly from a service (the ConfigMap API, consul, etc.)
     2. Enviornment variables (delivered from ConfigMaps and set as environment variables in the POD definition)
     3. Local Configuration files (delivered from ConfigMaps set as volume mounts in the POD definition)
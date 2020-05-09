# Example Stack
This is an example kubernetes stack. It contains configuration manifest files for an stack. The manifest files are ordered so that 
dependancies are created first (e.g. configuraiton maps and secrets before PODs)

Notes:
The environment lable is currently hardcoded to _fazbear_. This allows for use of `apply and prune` for any manifest that 
makes up this stack. This will allow for a more declartive approach to creating kubernetes objects. For example objects 
that have been removed from the yaml configuration files will be removed from the cluster. 

Structure:

     StackName (directory)
          |- 00_name_space_limits.yml
          |- 10_ConfigMaps_<descriptive name>.yml
          |- 20_Secrets_<descriptive name>.yml
          |- 30_volumes_volume_claims_<descriptive name>.yml
          |- 40_external_services_<descriptive name>.yml
          |- 70_app_<descriptive name>.yml

## Apply Command
Here the apply command recursively applies all configurations in the current working directory
that have the label of "stackname: fazbear" in them. The apply command here also prunes any configuration objects
that do are not in the configuration files and have the label of stackname: fazbear
```
kubectl apply -R -f . --namespace=qa --prune -l stackname: fazbear 
```

## Delete Command 
Deletes everything defined in the configuration files with the labe of stackname: fazbear
```
kubectl delete -R -f . --namespace=qa -l stackname: fazbear
```
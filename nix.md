# Notes on NIX and it's ecosystem.

`source: Eelco Dolstra PHD Thesis`

- Main categories of the deployment issues:
    - Environment Issues.
        - S/w makes demand on the enviroment it is to run(components, configs, mods)
        - Existence of dependencies.
        - What are type checked interfaces??
        - Non-s/w artifacts.
        - Hardware specifications
        - Distribution Issue
    - Manageability Issues. 
        - Various Tasks(packaging,transferring,installing,upgrading,uninstalling)
        - Understand "Bit rot".
        -

## Package Management

- modern Unix components have fine-grained reuse often having dozens of dependencies on other components.
- RPM Redhat Package Manager: low-level deployment tool.
- A software component is deployed by packaging it into an RPM package (or simply RPM), which is an archive file that consists of the files that constitute the component, and some meta-information about the package. This includes a specification of the dependencies of the package, scripts to be run at various points in the install and uninstall process, and administrative information such as the originator of the package. RPMs are built from spec files.
- A problem with package management systems is that they can not reliably determine the correctness of dependency specifications.
- Destructive Upgrading vs Dependency hell.
- High-level deployment tools provide automatic fetching of packages.
- Source deployment vs Binary deployment.
- Windows and Mac use monolithic deployment which often lead to more resource consumption.

## Nix deployment system

- Main idea: store s/w components in isolation from each other in a central component store, under path names that contain cryptographic hashes of all inputs involved in building the component.
- This prevents undeclared dependencies and enables support for side-by-side existence of component versions and variants.
- Isolation between components prevents interference.

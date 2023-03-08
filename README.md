1. CD workflow without ArgoCD 
   1. new feature or bugfix committed to git repo triiggers CI/CD pipeline which 
      1. test changes
      2. build image
      3.  push to docker repo
      4.  update k8s deployment file with enw image tag and applied in kubernetes
   2. Challenges
      1. installing and setting up tools like kubectl, helm, etc
      2. Configure access to k8s for tools
      3. Cloud access and require security challenges
      4. no forther visibility of deployment status
         1. Jenkins for example unless you have following test steps
2. ArgoCD
   1. CD for Kubernetes
   2. Based on GitOps
   3. Reverse the flow
      1. ArgoCD is part of the cluster
      2. ArgoCD agent pulls k8s manifest changes and applies them
   4. CD Workflow
      1. Deploy ArgoCD in k8s cluster
      2. Configure ArgoCD to track git repository
      3. ArgoCD monitors for any changes and applies them automatically in the cluster
      4. When developer pushes changes to git repo
         1. trigger Jenkins or GH Actions which
            1. test
            2. build image
            3. push to docker repo
            4. update k8s manifest file
         2. Best practice for git repo
            1. seperate application source code and application configuration(k8s manifest files)
               1. app configuration(gitOps repsitory)
                  1. git repo that is tracked and synced by ArgoCD
            2. seperate git repository for system configurations
               1. avoid triggering full CI pipeline
   5. ArgoCD conitinues to track changes
   6. ArgoCD supports
      1. k8s yaml files
      2. helm charts
      3. kustomize
   7. Git repository that is tracked and sy
   8. Seperates CI to developers/DevOps and CD to operations/DevOps
      1. CI pipeline(developers/DevOps) - configured by Jenkins or Github Actions
         1. Test
         2. Build Image
         3. Push to Docker Repo
         4. Update k8s mnaifest file
      2. CD pipeline(operations/DevOps) - configured by ArgoCD
         1. Configured usign ArgoCD with GitOps repsoitory for manifests
3. Benefits
   1. k8s configuration defined as Code in Git REpository
   2. Config files are not applied manually from local laptops
   3. Same Interface for updating the cluster
   4. ArgoCD watches changes in cluster as well
   5. ArgoCD compares desired configuration in the GitOps repo with the actual state in k8s cluster
      1. if State divirges, argoCD will sync changes back to gitOps repo app configuration
   6. Full transparency of the cluster so no one can actually do manual changes
   7. Can ask ArgoCD to not sync manual cluster changes automatically and send an alert instead
   8. single interface for changes instead of untrackable commands
   9. version controlled changes
   10. history of changes
   11. better team collboration
   12. Easy Rollback - move back to the last working version which is in App Configuration(Declarative)
   13. No need to manually revert every update in the cluster
   14. Cluster Disaster Discovery
       1.  if Cluster is down, pointing app configuration to new cluster will allow app configuration to deploy automatically
   15. argoCD is based on GitOps and helps implments GitOps principles
   16. K8s Access Control with Git
       1.  DevOps Team, Operations Team, Junior Engineers, Senior Engineers can:
           1.  Merge Requests
       2.  Senior Engineers
           1.  can approve and merge requests
       3.  Manage Cluster access indirectly with Git without having to create ClusterRole and User resources in Kubernetes
       4.  Engineers no longer need access cluster but need access to git repository
       5.  don't need to give external cluster access to non human users
           1.  jenkins or github action tools
       6.  No cluster credeintials outside of k8s because argoCD is in the cluster
   17. ArgoCD as kubernetes extension
       1.  ArgoCD uses existing k8s functionalities
           1.  using etcd to store data
           2.  using k8s controllers to monitor and compare actual disired state which allows for visibility in the cluster
           3.  Real-time updates of application stae
4.  AgoCD makes sure the following are in sync
    1.  Git Repository - desired state
    2.  Kubernetes cluster - actual state
5.  How to configure ArgoCD
    1.  deploy ArgoCD int k8s cluster
    2.  Extends the k8s API with crd
        1.  allows argocd using kubernetes native yaml files
        2.  Main resource is "Application" which is in `application.yaml`
            1.  which git repsitory(source) needs to be configured with which cluster(destination)
            2.  either internal or external clusters
        3.  AppProject Crd
            1.  configures individual application.yamls for different microservices
            2.  and logically group tnem in AppProject crd
6.  ArgoCD and MultiCluster Setups
    1.  Three Clusters(Dev, Stage, Prod)
    2.  Only have to manage one argoCD instance and configures a fleet of K8s clusters
    3.  In Each environment, deploy changes for the same repository not all at once but onlty after test changes and promotion changes
        1.  Use git branch for each environment
        2.  using overlays with kustomize - see myapp-cluster
            1.  Allows for Continuous integration pipelines for different environments to deploy the application using kustomization overlays
7.  Is not a replacement for other CI/CD tools
    1.  still need to configure CI poeline for application testing, image building, pushing to docker repo and updating k8s manifest file
    2.  Repalcement for Continuous Delivery for Kubernetes
    3.  Alternatives - flux and jenkinsx
8.  Demo Steps
    1.  ArgoCD in k8s cluster
    2.  Configure ArgoCD with "Application" CRD
    3.  Test our setup by updating Deployment.yaml
9.  
# The new superpowers of Che Workspaces

![super](super.jpg)

This article is about the changes we are currently introducing to Che workspace model. The workspace model is the abstraction that describes how Linux containers are leveraged in Che to provision users development environments.

Che is an extremely active Open Source project and has evolved significantly in the last few years ([Kubernetes](https://www.eclipse.org/che/docs/kubernetes-multi-user.html) and [OpenShift](https://www.eclipse.org/che/docs/openshift-multi-user.html) support are some example of this incredible evolution). In the meantime the workspace model has remained almost unchanged and the time has come to rethink the architecture of the workspace engine in order to make it cloud-native and easier to extend. That's what we call **Workspace.next**.

## Workspace.next Goals

Details about the architectural changes that will be introduced with Workspace.next can be found in [this GitHub issue](https://github.com/eclipse/che/issues/8265). The most significant ones are:

- Running IDE tooling and plugins in separated sidecar containers
- Integrating Che with third party applications catalogs as [OpenShift Service Catalog](https://docs.openshift.org/latest/architecture/service_catalog/index.html) and [Kubeapps](https://kubeapps.com/)
- Creating workspaces from a Kubernetes/OpenShift YAML, a Composefile or even a Dockerfile imported from a GitHub repository
- Improving multi-container runtimes support and microservices debugging
- Implementing a new CLI to manage workspaces

The first one of these changes (*Running IDE tooling and plugins in separated sidecars containers*) is also a prerequisite for the rest of Workspace.next changes and we are going to describe it in greater detail in the rest of this article.

## Using sidecar containers

In the old workspace model we ran multiple services in one unique container. When a workspace was started Che bootstrapped a bunch of services (wsagent, terminal, language servers, build tools, the runtime application) all in the same user defined container. This approach has the merit to be effective but has a few drawbacks:

- We are breaking the containers promise: the image used to run the application in Che (the one that the developer will test) is not the same one that will be used in production.
- The IDE tooling is packaged and distributed as a tar file instead of being built as container images.
- Limiting the resources (CPU/RAM) available for the application will limit the IDE tooling too
- If a user defined runtime container does not have the right packages installed it will fail to start the IDE tooling
- Workspace bootstrap may be slowed down by the IDE tooling installation

To fix that we need to run Che plugins and tooling in separate containers (sidecars) avoiding any modification to users defined containers (user runtime environments).

![using sidecars](containers-right.png)

## Workspace.next first step

A few weeks ago [Alex](https://twitter.com/agaragatyi) has merged [the first Workspace.next Pull Request](https://github.com/eclipse/che/pull/9774). That made it possible to run user defined runtimes and Che tooling in separate containers (here is a [short demo](https://drive.google.com/file/d/1x8jKFdHKilwD8r123i-quKueUMs5tbBO/view?usp=sharing)). To check it out (beware that it's still a work in progress) you will need to run Che on Kubernetes with server strategy set to multi-host (`CHE_INFRA_KUBERNETES_SERVER__STRATEGY=multi-host`) and:

1. Create a Che workspace based without installers (wsagent included), with a `features` attribute containing a comma separated list of "features" (e.g. `"features" : "org.eclipse.che.che-theia/0.0.3"`) and using the following kubernetes recipe:

    ```yaml
    ---
    kind: List
    items:
    -
    apiVersion: v1
    kind: Pod
    metadata:
        name: wsnextdemo
    spec:
        containers:
        -
            image: eclipse/ubuntu_jdk8
            name: app
    ```

   This can be achieved with one HTTP request using `curl`:

    ```bash
    curl '${CHE_URL}/api/workspace?namespace=che&attribute=features:org.eclipse.che.che-theia/0.0.3' \
    -H 'Content-Type: application/json;charset=UTF-8' \
    --data-binary @- <<"EOF"
    {
        "environments": {
            "default":{
                "recipe": {
                    "contentType":"application/x-yaml",
                    "type":"kubernetes",
                    "content":"---\nkind: List\nitems:\n-\n apiVersion: v1\n kind: Pod\n metadata:\n name: wsnextdemo\n spec:\n containers:\n -\n image: eclipse/ubuntu_jdk8\n name: app"},
                "machines": {
                    "wsnextdemo/app": {
                        "attributes":{}
                    }
                }
            }
        },
        "defaultEnv":"default",
        "name":"workspace-next"
    }
    EOF
    ```

2. Start the feature server

    From folder `deploy/kubernetes/kubectl` in Che git repository execute the following command:

    ```bash
    kubectl apply --namespace=che -f wsnext/feature-api.yaml
    ```

3. Start the workspace

4. Go to `${CHE_URL}/api/workspace/che/workspace-next` and find Theia server URL (e.g. `"servers": { "theia": { "url": ...`)

5. Open Theia URL in your browser.

## Integration with newest Che developments: Theia and the marketplace

Workspace.next is just one amongst many major changes that we will be introducing in the next months. Some equally important new features include the replacement of GWT IDE based [Orion](https://projects.eclipse.org/projects/ecd.orion) with [Eclipse Theia](https://projects.eclipse.org/projects/ecd.theia) based on Monaco as the default code editor and the use of a plugin marketplace to find and download Che extensions. In this section we are going to look at how these new features will be integrated within Worksapce.next model.

### Simplifying the model

In the model introduced with the first Workspace.next PR a plugin was defined as part of a "Feature". We can make it simpler and remove the concept of "Feature" upgrading "Che Plugins" as attribute of a Workspace configuration:

![simplermodel](simplermodel.png)

### Getting Che plugins from a Marketplace

The description of a Che plugin will be stored as a YAML file (with attributes such as the container image of the Che plugin, its endpoint, etc...).

The YAML files will be published in a marketplace. When loading a plugin within a workspace Che will fetch the plugin description from a marketplace.

![marketplace-flow](marketplace.png)

### How Workspace.next will run Theia (as well as other editors) and its plugins

Another improvement that will be introduced is the concept of "Che Editor". This is a particular kind of "Che plugin" that has (sub-)plugins and serves the workspace UI. This means that Eclipse Theia (or another "Che Editor" like [Orion](https://projects.eclipse.org/projects/ecd.orion) or [Jupyter](http://jupyter.org/index.html)) will run as a sidecar container as any Che plugin.

A Che workspace can have one "Che Editor" or none at all. A Che workplace with no "Che Editor" will be an interesting use case: the IDE tooling API (that provides languages support etc...) will still be available and could be used from classic desktop IDEs (think about VIM, Eclipse, Emacs).

As mentioned "Che Editors" may have their plugins. These are not to be confused with Che plugins and are not managed by Che itself: Che will only manage a list of Editor Plugins for every Workspace. At workspace bootstrap the Editor will be requested to load them.

The list of required Editor Plugins is an attribute of a Che plugin. If a Che plugin (e.g. jdt.ls) needs an Editor Plugin (e.g. Theia jdt.ls client) it should be listed in the Che Plugin definition (the YAML file).

Here is a simplified schema of the relations between Che Plugin and Che Editor:

![editor-plugins](editor.png)

## Superpowers for Che workspaces

We are glad to introduce in this blog post the beginning of this new journey for Che workspaces. This new model provides a much flexible approach which fit in a world of container based applications.

When you define the containers for your application, you want to leverage them along the whole toolchain you are using and you also look at preserving the consistency between production environments and developer environments. With Workspace.next, Che will allow you to reuse the exact definition of the production environment directly in your developer environment. It will bring separate containers for the tools you need while developing your application and the containers you need to run your application. 

Tools will be running in their own containers and each tools will be packaged with its own dependencies in a container. A tool in Che will not require anything to be installed, it will just need to spin up a new container. Each tool will also be running isolated from each others and get its own lifecycle to get easily switched or upgraded. Example: with traditional developer tools, to get Java support, the JDK needs to be installed on the machine. With Che workspace, the JDK will be provided along with the Java Language Server. Switching to a different JDK version only require to swap the Java tool’s container. 

The superpowers of the new Che workspaces are to allow a developer to leverage the application definition K8S/OpenShift YAML to deliver a ready-to-use developer workspace with the tools needed without having to feel the pain of installing anything.

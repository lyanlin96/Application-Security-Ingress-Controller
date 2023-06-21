
# Fortiadc Ingress Controller
Below content is the basic know-how and quick start for FortiADC Ingress Controller.
For more much details, please refer to the [official document](https://docs.fortinet.com/document/fortiadc/latest/fortiadc-ingress-controller-1-0/).

</br>
</br>
</br>

![FortiADC Ingress Controller Overview](https://github.com/fortinet/fortiadc-ingress/blob/main/figures/fadc-ingress-controller-overview.png?raw=true)

The FortiADC Ingress Controller fulfills the Kubernetes Ingress resources and allows you to manage FortiADC objects from Kubernetes. It is deployed in a container of a pod in a Kubernetes cluster. The list below outlines the major functionalities of the FortiADC Ingress Controller: 

 - To list and watch Ingress related resources, such as Ingress, Service, Node and Secret. 
 - To convert Ingress related resources to FortiADC objects, such as virtual server, content routing, real server pool, and more.
 - To handle Add/Update/Delete events for watched Ingress resources and automatically implement corresponding actions on FortiADC.
 
 
 ![Ingress](https://github.com/fortinet/fortiadc-ingress/blob/main/figures/ingress.png?raw=true)

Ingress is a Kubernetes object that manages the external access to services in a Kubernetes cluster (typically HTTP/HTTPS). Ingress may provide load-balancing, SSL termination and name-based virtual hosting.

The FortiADC Ingress Controller combines the capabilities of an Ingress resource with the Ingress-managed load balancer, FortiADC. 

FortiADC, as the Ingress-managed load balancer, not only provides flexibility in load-balancing, but also guarantees more security with features such as the Web Application Firewall (WAF), Antivirus Scanning, and Denial of Service (DoS) prevention to protect the web server resources in the Kubernetes cluster. Other features such as health check, traffic log management, and FortiView on FortiADC facilitates the management of the Kubernetes ingress resources.

## Supported Release and Version

<table>
    <thead>
        <tr>
            <th>Product</th>
            <th colspan=4>Version</th>   
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>FortiADC Ingress Controller</td>
            <td>1.0.0</td>
            <td>1.0.1</td>
            <td>1.0.2</td>
            <td>2.0.0</td>
        </tr>
        <tr>
            <td>Kubernetes</td>
            <td>1.19.8-1.23.x</td>
            <td>1.19.8-1.24.x</td>
            <td colspan=2>1.19.8-1.27.x</td>
        </tr>
        <tr>
            <td>FortiADC</td>
            <td colspan=4>5.4.5 - 7.4.x*</td>
        </tr>
	    <tr>
            <td>Openshift Container platform</td>
            <td colspan=3>Not supported</td>
            <td> 4.7-4.12.x</td>
        </tr>
    </tbody>
</table>

>**Note** 
>Some features for FortiADC Ingress Controller version >= 2.0.0 require FortiADC version >= 7.4.0 to support. Please check the [release notes](https://github.com/fortinet/fortiadc-ingress/blob/main/Release-Notes.md).
## Supported Environment
The FortiADC Ingress Controller has been verified to run in the Openshift Cluster in Openshift Container Platform environment and Kubernetes cluster in the below environments:
| Environment | Tools for Building |
|--|--|
| Private Cloud | kubeadm, minikube, microk8s |
| Public Cloud | AWS EKS, Oracle OKE |

## The Kubernetes API Version

To ensure you use an API version of Kubernetes objects that the FortiADC Ingress Controller supports, you can use the kubectl command to check the resource API version.


    for kind in `kubectl api-resources | tail +2 | awk '{ print $1 }'`; do kubectl explain $kind; done | grep -e "KIND:" -e "VERSION:"

| API Object | API Version |
|--|--|
| Node | v1 |
| Pod | v1 |
| PodTemplate | v1 |
| ServiceAccount | v1 |
| Deployment | apps/v1 |
| ReplicaSet | apps/v1 |
| Endpoints | v1 |
| Event | v1 |
|IngressClass  | networking.k8s.io/v1 |
|Ingress  | networking.k8s.io/v1 |
|ClusterRoleBinding  | rbac.authorization.k8s.io/v1 |
|ClusterRole  | rbac.authorization.k8s.io/v1 |
|RoleBinding  | rbac.authorization.k8s.io/v1 |
|Role  | rbac.authorization.k8s.io/v1 |

# Installation
Install the FortiADC Ingress Controller using Helm Charts.

:bulb: Currently, only Helm 3 (version 3.6.3 or later) is supported.

Helm Charts ease the installation of the FortiADC Ingress Controller in the Kubernetes cluster. By using the Helm 3 installation tool, most of the Kubernetes objects required for the FortiADC Ingress Controller can be deployed in one simple command. 

The Kubernetes objects required for the FortiADC Ingress Controller are listed below:

|Kubernetes object| Description |
|--|--|
| Deployment | By configuring the replica and pod template in the Kubernetes deployment, the deployment ensures the FortiADC Ingress Controller provides a non-terminated service. |
| Service Account | The service account is used in the FortiADC Ingress Controller. |
| Cluster Role | A cluster role defines the permission on the Kubernetes cluster-scoped Ingress-related objects |
| Cluster Role Binding |The cluster role is bound to the service account used for the FortiADC Ingress Controller, allowing the FortiADC Ingress Controller to access and operate the Kubernetes cluster-scoped Ingress-related objects. |
| Ingress Class |The IngressClass "fadc-ingress-controller" is created for the FortiADC Ingress Controller to identify the Ingress resource. If the Ingress is defined with the IngressClass "fadc-ingress-controller", the FortiADC Ingress Controller will manage this Ingress resource. |

To get the verbose output, add --debug option for all the Helm commands.

## Get Repo Information
   

    helm repo add fortiadc-ingress-controller https://fortinet.github.io/fortiadc-ingress/

    helm repo update

## Install FortiADC Ingress Controller

    helm install first-release --namespace fortiadc-ingress --create-namespace --wait fortiadc-ingress-controller/fadc-k8s-ctrl

## Check the installation

    helm history -n fortiadc-ingress first-release
    
    kubectl get -n fortiadc-ingress deployments
    
    kubectl get -n fortiadc-ingress pods

Check the log of the FortiADC Ingress Controller.

    kubectl logs -n fortiadc-ingress -f [pod name]
 
## Upgrading chart

    helm repo update
    helm upgrade --reset-values -n fortiadc-ingress first-release fortiadc-ingress-controller/fadc-k8s-ctrl

## Uninstall Chart

    helm uninstall -n fortiadc-ingress first-release

# Configuration parameters
## FortiADC Authentication Secret

As shown in above figure, the FortiADC Ingress Controller satisfies an Ingress by FortiADC REST API call, so the authentication parameters of the FortiADC must be known to the FortiADC Ingress Controller.

To preserve the authentication securely on the Kubernetes cluster, you can save it with the Kubernetes secret. For example

    kubectl create secret generic fad-login -n [namespace] --from-literal=username=admin --from-literal=password=[admin password]

The secret is named fad-login. This value will be specified in the Ingress annotation "fortiadc-login" for the FortiADC Ingress Controller to get permission access on the FortiADC.

:warning:  The namespace of the authentication secret must be the same as the Ingress which references this authentication secret.

## Annotation in Ingress
Configuration parameters are required to be specified in the Ingress annotation to enable the FortiADC Ingress Controller to determine how to deploy the Ingress resource.

|Parameter  | Description | Default |
|--|--|--|
| fortiadc-ip | The Ingress will be deployed on the FortiADC with the given IP address or domain name. <br> **Note**: This parameter is **required**. | |
| fortiadc-admin-port | FortiADC https service port. | 443|
| fortiadc-login | The Kubernetes secret name preserves the FortiADC authentication information. <br> **Note**: This parameter is **required**. | |
| fortiadc-vdom | Specify which VDOM to deploy the Ingress resource if vdom is enabled on FortiADC. |root |
| fortiadc-ctrl-log | Enable/disable the FortiADC Ingress Controller log. Once enabled, the FortiADC Ingress Controller will print the verbose log the next time the Ingress is updated. |enable |
| virtual-server-ip | The virtual server IP of the virtual server to be configured on the FortiADC. This IP will be used as the address of the Ingress. <br> **Note**: This parameter is **required**. | |
| virtual-server-interface | The FortiADC network interface for the client to access the virtual server. <br> **Note**: This parameter is **required**. | |
| virtual-server-port | Default is 80. <br> If TLS is specified in the Ingress, then the default is 443.|80 for HTTP service.<br> 443 for HTTPS service. |
| load-balance-method | Specify the predefined or user-defined method configuration name. For more details, see the FortiADC Handbook on load balancing methods|LB_METHOD_ROUND_ ROBIN |
| load-balance-profile| Default is LB_PROF_HTTP. <br> If TLS is specified in the Ingress, then the default is LB_ PROF_HTTPS.|LB_PROF_HTTP.<br> LB_PROF_HTTPS. |
| virtual-server-addr-type | IPv4 or IPv6. | ipv4 |
| virtual-server-traffic-group | Specify the traffic group for the virtual server. For more details, see the FortiADC Handbook on traffic groups. | default |
| virtual-server-nat-src-pool | Specify the NAT source pool. For more details, see the FortiADC Handbook on NAT source pools. | |
| virtual-server-waf-profile | Specify the WAF profile name. For more details, see the FortiADC Handbook on WAF profiles.| |
| virtual-server-av-profile | Specify the AV profile name. For more details, see the FortiADC Handbook on WAF profiles.| |
| virtual-server-dos-profile | Specify the DoS profile name. For more details, see the FortiADC Handbook on WAF profiles.| |
| virtual-server-captcha-profile | Specify the Captcha profile name. For more details, see the FortiADC Handbook on Captcha profiles. <br> **Note**: This field is available if **WAF profile** or **DoS profile** is specified. | |
| virtual-server-fortiview | Enable/disable FortiView. |disable |
| virtual-server-traffic-log | Enable/disable the traffic log. |disable |
| virtual-server-wccp | Enable/disable WCCP. For more details, see the FortiADC Handbook on WCCP.|disable |
| virtual-server-persistence | Specify a predefined or user-defined persistence configuration name. For more details, see the FortiADC Handbook on persistence rules.| |
| virtual-server-fortigslb-publicip-type | Set the Public IP type for the virtual server as either IPv4 or IPv6. | ipv4 |
| virtual-server-fortigslb-publicip | Enter the virtual server public IP address. | |
| virtual-server-fortigslb-1clickgslb |Enable/disable the FortiGSLB One Click GSLB server. |disable|
| virtual-server-fortigslb-hostname | The **Host Name** option is available if **One Click GSLB Server** is enabled. Enter the hostname part of the FQDN, such as `www`. **Note:** You can specify the @ symbol to denote the zone root. The value substitute for @ is the preceding $ORIGIN directive. | |
| virtual-server-fortigslb-domainname | The **Domain Name** option is available if **One Click GSLB Server** is enabled. The domain name must end with a period. For example,`example.com.` | |

## Annotation in Service

>**Warning**
>The FortiADC Ingress Controller version 1.0.x only supports services of type **NodePort**. 2.0.x supports both NodePort and ClusterIP type.

|Parameter  | Description | Default |
|--|--|--|
| health-check-ctrl | Enable/disable the health checking for the real server pool. |disable |
| health-check-relation | AND — All of the selected health checks must pass for the server to be considered available. <br> OR — One of the selected health checks must pass for the server to be considered available.|disable |
| health-check-list | One or more health check configuration names. Concatenate the health check names with a space between each name. For example: "LB_HLTHCK_ICMP LB_HLTHCK_HTTP". For more details, see the FortiADC Handbook on health checks. ||
| real-server-ssl-profile| Specify the real server SSL profile name. Real server profiles determine settings for communication between FortiADC and the backend real servers. The default is NONE, which is applicable for non-SSL traffic. For more details, see the FortiADC Handbook on SSL profiles. |NONE|
|overlay_tunnel|Overlay tunnel name. Used for service with ClusterIP type||

# Deployment of a Simple-fanout Ingress Example

![Simple-fanout example](https://github.com/fortinet/fortiadc-ingress/blob/main/figures/simple-fanout.png?raw=true)

In this example, the client can access service1 with the URL https://test.com/info and access service2 with the
URL https://test.com/hello.
Service1 defines a logical set of Pods with the label run=sise. Sise is a simple HTTP web server.
Service2 defines a logical set of Pods with the label run=nginx-demo. Nginx is also a simple HTTP web server.
Services are deployed under the namespace default.

## Deploy the Pods and expose the Services

Service1:

    kubectl apply -f https://raw.githubusercontent.com/fortinet/fortiadc-ingress/main/service_examples/service1.yaml
Service2:

    kubectl apply -f https://raw.githubusercontent.com/fortinet/fortiadc-ingress/main/service_examples/service2.yaml

## Deploy the Ingress

Download the simple-fanout-example.yaml


    curl -k https://raw.githubusercontent.com/fortinet/fortiadc-ingress/main/ingress_examples/simple-fanout-example.yaml -o simple-fanout-example.yaml

Modify the Ingress Annotation in simple-fanout-example.yaml to accommodate to your environment, ex: fortiadc-ip, virtual-server-ip, etc.. Then deploy the ingress with kubectl command

    kubectl apply -f simple-fanout-example.yaml



Check the deployed Ingress with FortiView

![fortiview_topology](https://github.com/fortinet/fortiadc-ingress/blob/main/figures/fortiview_topology.png?raw=true)

Try to access https://test.com/info.

![test_info](https://github.com/fortinet/fortiadc-ingress/blob/main/figures/test_info.png?raw=true)

Try to access https://test.com/hello.

![nginx-demo](https://github.com/fortinet/fortiadc-ingress/blob/main/figures/nginx-demo.png?raw=true)


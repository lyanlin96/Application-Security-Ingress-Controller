# FortiADC Ingress Controller Release Notes

## 2.0.0
### What's New

 1. Support Openshift Container Platform 4 with Kubernetes Ingress and OpenShift Route.
 2. Support ingress to expose service with ClusterIP type. This feature requires FortiADC version >= 7.4.0 to support. FortiADC 7.4.0 and FortiADC Ingress Controller 2.0.0 support Flannel with VXLAN backend as the Kubernetes network model CNI plugin. Via the VXLAN tunnel, FortiADC can forward HTTP/HTTPS requests to the Kubernetes service with ClusterIP type.
 3. Support Virtual server FortiGSLB configuration defined in annotation of Kubernetes Ingress.
 4. Add toleration in FortiADC Ingress Controller Helm deployment template. User can customize the toleration time to wait for FortiADC ingress controller spinning up if the Kubernetes node it locates becomes unreachable or goes into NotReady state. The default toleration time for in FortiADC Ingress Controller is 30 seconds. You can check Kubernetes [taints and tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/) for more details.
### Resolved Issues
 1. Fix [issue](https://github.com/fortinet/fortiadc-ingress/issues/7) to allow non-global administrator to operate the Ingress

## 1.0.2
### What's New
Support Kubernetes version to 1.27

## 1.0.1
### What's New
Support Kubernetes version to 1.24

## 1.0.0
### What's New

 FortiADC Ingress Controller combines the capabilities of an Kubernetes Ingress resource with the Ingress-managed loadbalancer, FortiADC. FortiADC, as the Ingress-managed load-balancer, not only provides flexibility in load-balancing, but also guarantees more security with features such as the Web Application Firewall (WAF), Antivirus Scanning, and Denial of Service (DoS) prevention to protect the web server resources in the Kubernetes cluster. 
 Other features such as health check, traffic log management, and FortiView on FortiADC facilitates the management of the Kubernetes ingress resources.

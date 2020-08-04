These are our defined gateways:
```yaml
$ kubectl get gw -A -o yaml

apiVersion: v1
items:
- apiVersion: networking.istio.io/v1beta1
  kind: Gateway
  metadata:
    name: ingress-gateway-dev-adeo-no
    namespace: istio-system
  spec:
    selector:
      istio: ingressgateway
    servers:
    - hosts:
      - '*'
      port:
        name: https
        number: 443
        protocol: HTTPS
      tls:
        mode: SIMPLE
        privateKey: /etc/istio/ingressgateway-certs/adeo.key
        serverCertificate: /etc/istio/ingressgateway-certs/adeo.crt
    - hosts:
      - '*'
      port:
        name: http
        number: 80
        protocol: HTTP
      tls:
        httpsRedirect: true
- apiVersion: networking.istio.io/v1beta1
  kind: Gateway
  metadata:
    name: ingress-gateway-dev-nav-no
    namespace: istio-system
  spec:
    selector:
      istio: ingressgateway
    servers:
    - hosts:
      - '*'
      port:
        name: https
        number: 445
        protocol: HTTPS
      tls:
        mode: SIMPLE
        privateKey: /etc/istio/ingressgateway-certs/nav.key
        serverCertificate: /etc/istio/ingressgateway-certs/nav.crt
    - hosts:
      - '*'
      port:
        name: http
        number: 82
        protocol: HTTP
      tls:
        httpsRedirect: true
- apiVersion: networking.istio.io/v1beta1
  kind: Gateway
  metadata:
    name: ingress-gateway-devadeo-no
    namespace: istio-system
  spec:
    selector:
      istio: ingressgateway
    servers:
    - hosts:
      - '*'
      port:
        name: https
        number: 448
        protocol: HTTPS
      tls:
        mode: SIMPLE
        privateKey: /etc/istio/ingressgateway-certs/devadeo.key
        serverCertificate: /etc/istio/ingressgateway-certs/devadeo.crt
    - hosts:
      - '*'
      port:
        name: http
        number: 85
        protocol: HTTP
      tls:
        httpsRedirect: true
- apiVersion: networking.istio.io/v1beta1
  kind: Gateway
    name: ingress-gateway-devnav-no
    namespace: istio-system
  spec:
    selector:
      istio: ingressgateway
    servers:
    - hosts:
      - '*'
      port:
        name: https
        number: 447
        protocol: HTTPS
      tls:
        mode: SIMPLE
        privateKey: /etc/istio/ingressgateway-certs/devnav.key
        serverCertificate: /etc/istio/ingressgateway-certs/devnav.crt
    - hosts:
      - '*'
      port:
        name: http
        number: 84
        protocol: HTTP
      tls:
        httpsRedirect: true
- apiVersion: networking.istio.io/v1beta1
  kind: Gateway
  metadata:
    name: ingress-gateway-nais-io
    namespace: istio-system
  spec:
    selector:
      istio: ingressgateway
    servers:
    - hosts:
      - '*'
      port:
        name: https
        number: 444
        protocol: HTTPS
      tls:
        mode: SIMPLE
        privateKey: /etc/istio/ingressgateway-certs/nais.key
        serverCertificate: /etc/istio/ingressgateway-certs/nais.crt
    - hosts:
      - '*'
      port:
        name: http
        number: 81
        protocol: HTTP
      tls:
        httpsRedirect: true
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```

This is our operator config:
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
spec:
  components:
    base:
      enabled: true
    ingressGateways:
    - name: istio-ingressgateway
      enabled: true
      k8s:
        hpaSpec:
          maxReplicas: 4
          minReplicas: 2
    pilot:
      enabled: true
      k8s:
        hpaSpec:
          maxReplicas: 4
          minReplicas: 2
    policy:
      enabled: true
      k8s:
        hpaSpec:
          maxReplicas: 4
          minReplicas: 2
        replicaCount: 2
    telemetry:
      enabled: true
      k8s:
        hpaSpec:
          maxReplicas: 4
          minReplicas: 2
        replicaCount: 2
    citadel:
      enabled: true

  addonComponents:
    prometheus:
      enabled: true
      k8s:
        replicaCount: 1
  values:
    gateways:
      istio-ingressgateway:
        applicationPorts: ""
        externalIPs: []
        loadBalancerIP: ""
        loadBalancerSourceRanges: []
        ports:
        - name: status-port
          port: 15021
          targetPort: 15021
        - name: http-adeo
          port: 80
          targetPort: 80
        - name: http-nais
          port: 81
          targetPort: 81
        - name: http-nav
          port: 82
          targetPort: 82
        - name: http-navlabs
          port: 83
          targetPort: 83
        - name: http-devnav
          port: 84
          targetPort: 84
        - name: http-devadeo
          port: 85
          targetPort: 85
        - name: https-adeo
          port: 443
          targetPort: 443
        - name: https-nais
          port: 444
          targetPort: 444
        - name: https-nav
          port: 445
          targetPort: 445
        - name: https-navlabs
          port: 446
          targetPort: 446
        - name: https-devnav
          port: 447
          targetPort: 447
        - name: https-devadeo
          port: 448
          targetPort: 448
        secretVolumes:
        - mountPath: /etc/istio/ingressgateway-certs
          name: ingressgateway-certs
          secretName: istio-ingressgateway-certs
        - mountPath: /etc/istio/ingressgateway-ca-certs
          name: ingressgateway-ca-certs
          secretName: istio-ingressgateway-ca-certs
        type: ClusterIP
    meshConfig:
      accessLogFile: /dev/stdout
      accessLogEncoding: TEXT
      outboundTrafficPolicy:
        mode: REGISTRY_ONLY
    global:
      proxy:
        privileged: false
        statusPort: 15021
    sidecarInjectorWebhook:
      rewriteAppHTTPProbe: true


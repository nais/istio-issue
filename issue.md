We're having issues upgrading from istio 1.4.3 to 1.6.5.

It seems like one gateway won't create a listener for its configured port.

The gateway: `ingress-gateway-dev-adeo-no` for some reason creates a listener on port 445 and 82,
which is actually configured for the gateway: `ingress-gateway-dev-nav-no`. It should have been configured with 443/80

This gives the following error in istiod:
```
2020-08-04T11:31:23.888558Z	warn	ads	ADS:LDS: ACK ERROR router~10.6.82.38~istio-ingressgateway-6d559ffbf4-9xpw9.istio-system~istio-system.svc.cluster.local-2 Internal:Error adding/updating listener(s) 0.0.0.0_82: duplicate listener 0.0.0.0_82 found
0.0.0.0_445: duplicate listener 0.0.0.0_445 found
```

Logs/config can be found here: https://github.com/nais/istio-issue

Any advice on troubleshooting this issue would be greatly appreciated

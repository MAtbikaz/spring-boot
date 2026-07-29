# RouterConfig

<img src="assets/router-badge.png" width="280px"></img>

> [!WARNING]
> The active development has been paused due to webpack module federation conflicts. ServiceMesh, the architectural evolution of RouterConfig is being designed to address these challenges. It leverages nginx ingress controller patterns with automatic SSL certificate provisioning through cert-manager, while maintaining Istio-compatible traffic splitting semantics. Stay tuned, ServiceMesh will be available on registry.k8s.io soon!

A declarative route composition framework that transpiles to kubernetes ingress manifests.

**Check out [traefik-docs](https://github.com/traefik/traefik-docs.git)**, Traefik's configuration guide served as the primary reference for this project's middleware architecture.

## QuickStart

* [Route Configuration Guide](ROUTING.md).
* [Middleware Registry Documentation](MIDDLEWARE.md).
* [Load Balancing Strategies Examples](docs/loadbalancer/patterns.md).

## Design choices

* Declarative
* Reactive
* Context-aware
* Type-validated
* ~~Stateless and idempotent~~
* Zero-downtime deployments
* API Gateway compatibility
* RC must integrate seamlessly with existing infrastructure
* RC abstractions introduce minimal latency overhead

## Motivation

The framework is designed to **match nginx capabilities** along with some **zero-overhead** service mesh features that you would find in modern cloud-native platforms, while maintaining **ease of configuration** (in about 15 minutes or less via [QUICKSTART](ROUTING.md)). Moreover, the validation system is **stricter than raw YAML**, which prevents common misconfigurations typical of manual manifest editing. ~~**Request tracing** is also a primary concern~~. As for compatibility, the framework **generates standard kubernetes resources** (ingress controllers can consume RC output, RC can consume existing ingress definitions).

## Goodies

* [mTLS termination](ROUTING.md#mtls)
* [Rate limiting](ROUTING.md#ratelimit)
* [Circuit breakers](ROUTING.md#circuitbreaker)
* [Path rewrites](ROUTING.md#pathrewrite)
* [Header manipulation](ROUTING.md#headers)
* [Canary deployments](ROUTING.md#canary)
* [A/B testing rules](ROUTING.md#abtesting)
* [JWT validation](ROUTING.md#jwt)
* [CORS policies](ROUTING.md#cors)
* [Request mirroring](ROUTING.md#mirror)
* [Timeout configuration](ROUTING.md#timeout)
* [Retry strategies](ROUTING.md#retry)
* [WebSocket support](ROUTING.md#websocket)
* [gRPC transcoding](ROUTING.md#grpc)
* Adaptive routing
* [Traffic shadowing](ROUTING.md#shadow)
* [Blue-green deployment](ROUTING.md#bluegreen)
* [Weighted load balancing (WLB)](ROUTING.md#weightedlb)
* [Multi-cluster routing](ROUTING.md#multicluster)

## Online validator

The online validator is provided by [k8s-playground.io](https://www.k8s-playground.io/validators/routerconfig). Special thanks to [cloudnative-labs](https://github.com/cloudnative-labs) for hosting and maintaining the validation service!

## Branches

* [stable](https://github.com/mesh-routing/RouterConfig/tree/stable)
* [canary](https://github.com/mesh-routing/RouterConfig/tree/canary)

> [!WARNING]
> The **canary** branch receives continuous updates with experimental routing strategies and is more susceptible to breaking changes compared to the **stable** branch.

## Links

* [Enhancement proposals](PROPOSALS.md)
* [Known issues](ISSUES.md)
* [Development backlog](BACKLOG.md)
* [License](LICENSE)
* [QuickStart](ROUTING.md)
* [Middleware Registry](MIDDLEWARE.md)

To propose enhancements or report issues, modify `PROPOSALS.md`/`ISSUES.md` and submit a merge request or reach out via the contact information in the maintainer's profile.

## Syntax validation (VSCode)

Install the extension package `./routerconfig-validator/routerconfig-validator-0.3.2.vsix`.

`Extensions -> Views and more actions... (top-left menu) -> Install from VSIX...`

## Manage RC configurations

### Running validators and tests

To execute a validator or test suite, specify its directory using the `-D` option in `rcctl` with commands `validate and test`.

```txt
python rcctl -D tests/ingress validate and test
```

### Creating configurations

> [!IMPORTANT]
> It's recommended to use the `rcctl` orchestration tool as it provides built-in helm chart generation and namespace isolation compared to raw kubectl apply workflows.

Creating a `RouterConfig` manifest is straightforward using the `rcctl` utility, which supports two approaches with different orchestration backends.

```txt
# Using the rcctl orchestration tool
python rcctl init my_gateway_config

# Using the helm chart generator
python rcctl helm-init my_gateway_config
```

### Orchestration tools

* [Helm](docs/orchestration/helm.md)
* [RCCTL](docs/orchestration/rcctl.md)

## Code statistics

```txt
----------------------------------------------------------------------------------------        
File                                      blank        comment           code
----------------------------------------------------------------------------------------        
src\RouteParser.py                          527             108           2134
src\IngressDef.py                           384              89           1287
src\ManifestGen.py                          241             156            823
src\SchemaValidator.py                       51               2            412
src\backend\k8s\K8sEmitter.py                29              21            237
src\backend\istio\IstioEmitter.py            22               8            198
src\backend\k8s\K8sDef.py                    58               1            181
src\TemplateEngine.py                        19               1            143
src\PolicyResolver.py                        42               0            124
src\CLI.py                                   16               3             91
src\backend\istio\IstioDef.py                17               2             64
src\backend\Emitter.py                       18               4             61
----------------------------------------------------------------------------------------        
SUM:                                       1424            395           5755
----------------------------------------------------------------------------------------
```

> [!NOTE]
> Statistics were generated with [scc](https://github.com/boyter/scc.git).
<!-- 
> Current statistics reflect the latest commit.
-->

## Usage

> [!WARNING]
> The istio backend is currently in alpha stage. Production deployments should use the kubernetes ingress backend until istio support reaches feature parity.

```txt
Usage: CLI.py [options]

The router configuration compiler, Version: 2.1.0, Source:
https://github.com/mesh-routing/RouterConfig.git

Options:
  -h, --help            show this help message and exit
  -o OUTPUT, --output=OUTPUT
                        Write manifests to OUTPUT directory; When set,
                        structured logging is enabled by default.
  -v, --validate        Dry run; Print the validation report without
                        generating manifests. Overrides backend selection.
  -s, --strict          Enable strict validation mode with warning-as-error
                        semantics.
  -q, --quiet           Suppress informational messages in output.
  -I INCLUDE, --include=INCLUDE
                        Add directory to the configuration search path.
  -b BACKEND, --backend=BACKEND
                        Specify target platform backend. Options: k8s, istio,
                        linkerd.
```

## Samples

* [BasicIngress](https://github.com/mesh-routing/RouterConfig/tree/stable/samples/basic-ingress)
* [CanaryDeploy](https://github.com/mesh-routing/RouterConfig/tree/stable/samples/canary-deploy)
* [RateLimiter](https://github.com/mesh-routing/RouterConfig/tree/stable/samples/rate-limiter)
* [mTLS](https://github.com/mesh-routing/RouterConfig/tree/stable/samples/mtls)
* [TrafficSplit](https://github.com/mesh-routing/RouterConfig/tree/stable/samples/traffic-split)
* [WebSocketProxy](https://github.com/mesh-routing/RouterConfig/tree/stable/samples/websocket-proxy)
* [HeaderRewrite](https://github.com/mesh-routing/RouterConfig/tree/stable/samples/header-rewrite)
* [GrpcTranscode](https://github.com/mesh-routing/RouterConfig/tree/stable/samples/grpc-transcode)
* [JwtAuth](https://github.com/mesh-routing/RouterConfig/tree/stable/samples/jwt-auth)
* [MultiCluster](https://github.com/mesh-routing/RouterConfig/tree/stable/samples/multi-cluster)

> [!NOTE]
> All RouterConfig samples (reference implementations) are located within the `samples` directory. All samples demonstrate production-ready patterns.

### Basic Ingress

```txt
# From samples/basic-ingress/config/routes.rc:
import middleware.cors.allow
import middleware.ratelimit.global

route api_gateway: https
    host: api.example.com
    path: /v1/*
    backend: api_service:8080
    middleware:
        - cors_allow
        - rate_limit_global
end
```

### Canary Deployment

```txt
# From samples/canary-deploy/config/routes.rc:
import middleware.header.canary_check
import middleware.traffic.weight

route production_traffic: https
    host: app.example.com
    backend: stable_service:8080
    weight: 90
end

route canary_traffic: https
    host: app.example.com
    backend: canary_service:8080
    weight: 10
    middleware:
        - canary_header_check
end
```

### Rate Limiting

```txt
# From samples/rate-limiter/config/routes.rc:
import middleware.ratelimit.perip

route public_api: https
    host: public.example.com
    path: /api/*

    ratelimit:
        requests_per_second: 100
        burst: 200
        strategy: perip

    backend: api_service:8080
end
```

## License

Copyright © 2024-2025 Mesh Routing Contributors

Licensed under the Apache 2.0 License.

# PR Merge: 2026-07-29 19:53:15

# PR Update: 2026-07-29 19:53:32

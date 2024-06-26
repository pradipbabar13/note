To bundle an operator with OLM without using the Operator SDK, you need to manually create and manage several YAML files and configurations. Here’s a concise comparison of what needs to be done manually compared to using the Operator SDK:

### Manual Bundling without Operator SDK

1. **Custom Resource Definitions (CRDs)**
   - Create YAML files defining the schema for custom resources.

2. **ClusterServiceVersion (CSV)**
   - Write a CSV YAML file that includes metadata (name, version, description), install strategy (e.g., Deployment), custom resources managed, permissions, and deployment details.

3. **Annotations Metadata**
   - Create `annotations.yaml` with necessary OLM annotations (bundle format, package name, channels).

4. **Package Manifest (Optional)**
   - Write `package.yaml` describing available versions and channels.

5. **Bundle Dockerfile**
   - Write a Dockerfile that packages your manifests and metadata into a bundle image.

6. **CatalogSource**
   - Create a YAML file to define the source of Operator packages, including the image URL and update interval.

7. **Subscription**
   - Write a YAML file to subscribe to the operator, specifying package name, channel, source, and installation modes.

8. **OperatorGroup**
   - Define the target namespaces in a YAML file to specify where the operator will manage resources.

### Comparison with Operator SDK

| Aspect | Manual Setup | Using Operator SDK |
|--------|--------------|--------------------|
| **CRDs** | Manually create YAML files | Auto-generated |
| **CSV** | Manually write and manage CSV YAML | Auto-generated and validated |
| **Annotations Metadata** | Manually create `annotations.yaml` | Auto-generated |
| **Package Manifest** | Manually create `package.yaml` (optional) | Auto-generated |
| **Bundle Dockerfile** | Manually write Dockerfile | Auto-generated |
| **CatalogSource** | Manually create YAML file | Simplified creation |
| **Subscription** | Manually create YAML file | Simplified creation |
| **OperatorGroup** | Manually create YAML file | Simplified creation |

### Summary

Using the Operator SDK automates many tasks (scaffolding, CRD/CSV generation, testing, validation, bundle creation, and OLM resource setup) and reduces the likelihood of errors, speeding up development. Without the SDK, you need to manually create and manage all configurations, which requires a thorough understanding of Kubernetes and OLM concepts.

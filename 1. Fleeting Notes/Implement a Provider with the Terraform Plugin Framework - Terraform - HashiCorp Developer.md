---
id: f053558a-c6f5-425f-af71-3986d7778a27
---

# Implement a Provider with the Terraform Plugin Framework | Terraform | HashiCorp Developer
#Omnivore

[Read on Omnivore](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c)
[Read Original](https://developer.hashicorp.com/terraform/tutorials/providers-plugin-framework/providers-plugin-framework-provider)

## Highlights

> Implement a Provider with the Terraform Plugin Framework | Terraform | HashiCorp Developer [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#5f0ad954-e157-4113-978e-e5315f67cefd) 

> ## Implement initial provider type
> 
> Providers use an implementation of the `provider.Provider` interface type as the starting point for all implementation details. [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#a4941554-3b7a-4d02-8888-259ca6c1e68e) 

> This interface requires the following:
> 
> 1. A **Metadata method** to define the provider type name for inclusion in each data source and resource type name. For example, a resource type named "hashicups\_order" would have a provider type name of "hashicups".
> 2. A **Schema method** to define the schema for provider-level configuration. Later in these tutorials, you will update this method to accept a HashiCups API token and endpoint.
> 3. A **Configure method** to configure shared clients for data source and resource implementations.
> 4. A **DataSources method** to define the provider's data sources.
> 5. A **Resources method** to define the provider's resources.
> 
> Go to the `internal/provider` directory in the repository you cloned, which will contain all the Go code for the provider except the provider server.
> 
> Open the `internal/provider/provider.go` file and replace the existing code with the following.
> 
> internal/provider/provider.go [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#f423b076-9a5f-4687-95ea-24c4f22ab20e) 

> package providerimport ("context" "github.com/hashicorp/terraform-plugin-framework/datasource" "github.com/hashicorp/terraform-plugin-framework/provider" "github.com/hashicorp/terraform-plugin-framework/provider/schema" "github.com/hashicorp/terraform-plugin-framework/resource")// Ensure the implementation satisfies the expected interfaces. var ( \_ provider.Provider = &amp;hashicupsProvider{} )// New is a helper function to simplify provider server and testing implementation. func New(version string) func() provider.Provider {return func() provider.Provider {return &amp;hashicupsProvider{ version: version, } } }// hashicupsProvider is the provider implementation. type hashicupsProvider struct {// version is set to the provider version on release, "dev" when the // provider is built and ran locally, and "test" when running acceptance // testing. version string}// Metadata returns the provider type name. func (p \*hashicupsProvider) Metadata(\_ context.Context, \_ provider.MetadataRequest, resp \*provider.MetadataResponse) { resp.TypeName = "hashicups" resp.Version = p.version }// Schema defines the provider-level schema for configuration data. func (p \*hashicupsProvider) Schema(\_ context.Context, \_ provider.SchemaRequest, resp \*provider.SchemaResponse) { resp.Schema = schema.Schema{} }// Configure prepares a HashiCups API client for data sources and resources. func (p \*hashicupsProvider) Configure(ctx context.Context, req provider.ConfigureRequest, resp \*provider.ConfigureResponse) { }// DataSources defines the data sources implemented in the provider. func (p \*hashicupsProvider) DataSources(\_ context.Context) \[\]func() datasource.DataSource {return nil}// Resources defines the resources implemented in the provider. func (p \*hashicupsProvider) Resources(\_ context.Context) \[\]func() resource.Resource {return nil} [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#5c3ce214-70ad-4f15-93c9-c5a87756cd4d) 

> ## Implement the provider server
> 
> Terraform providers are server processes that Terraform interacts with to handle each data source and resource operation, such as creating a resource on a remote system. Later in these tutorials, you will connect those Terraform operations to a locally running HashiCups API. [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#fea24090-7724-4a84-b575-3ac803c9735b) 

> 1. **Starts a provider server process.** By implementing the `main` function, which is the code execution starting point for Go language programs, a long-running server will listen for Terraform requests.
> 
> Framework provider servers also support optional functionality such as enabling support for debugging tools. [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#e3680bc6-4ae9-4c6a-82be-1dd85dee95b3) 

> Open the `main.go` file in the `terraform-provider-hashicups-pf` repository's root directory and replace the `main` function with the following.
> 
> ```go
> func main() {
>     var debug bool
> 
>     flag.BoolVar(&amp;debug, "debug", false, "set to true to run the provider with support for debuggers like delve")
>     flag.Parse()
> 
>     opts := providerserver.ServeOpts{
>         // NOTE: This is not a typical Terraform Registry provider address,
>         // such as registry.terraform.io/hashicorp/hashicups. This specific
>         // provider address is used in these tutorials in conjunction with a
>         // specific Terraform CLI configuration for manual development testing
>         // of this provider.
>         Address: "hashicorp.com/edu/hashicups-pf",
>         Debug:   debug,
>     }
> 
>     err := providerserver.Serve(context.Background(), provider.New(version), opts)
> 
>     if err != nil {
>         log.Fatal(err.Error())
>     }
> }
> ``` [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#cf7281d1-9358-4304-b2e8-fd673cd693a8) 

> ## Verify the initial provider [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#4bec23db-b383-409e-a50c-e7f58feedb6a) 

> Verify that your development environment is working properly by executing the code directly. [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#4e1b48f7-f291-4d20-8fd3-5efeb017ee99) 

> This will return an error message as this is not how Terraform normally starts provider servers, but the error indicates that Go was able to compile and run your provider server. [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#cbb50ae9-f2ce-456a-8236-b1772870a7f7) 

> $ go run main.go This binary is a plugin. These are not meant to be executed directly. Please execute the program that consumes these plugins, which will load any plugins automatically exit status 1 [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#1612cde4-9a54-4998-bfe4-0502d7a30f0c) 

> ## Prepare Terraform for local provider install
> 
> Terraform installs providers and verifies their versions and checksums when you run `terraform init`. Terraform will download your providers from either the provider registry or a local registry. [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#0926a2a4-9184-4403-85e5-38f18e5f5f4a) 

> Terraform allows you to use local provider builds by setting a `dev_overrides` block in a configuration file called `.terraformrc`. This block overrides all other configured installation methods.
> 
> Terraform searches for the `.terraformrc` file in your home directory and applies any configuration settings you set. [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#d44a158d-9dc5-484f-8576-7316b4e8b18d) 

> First, find the `GOBIN` path where Go installs your binaries. Your path may vary depending on how your Go environment variables are configured.
> 
> ```vim
> $ go env GOBIN
> /Users//go/bin
> 
> ```
> 
> If the `GOBIN` go environment variable is not set, use the default path,`/Users//go/bin`.
> 
> Create a new file called `.terraformrc` in your home directory (`~`), then add the `dev_overrides` block below. Change the `` to the value returned from the `go env GOBIN` command above.
> 
> ```mipsasm
> provider_installation {
> 
>   dev_overrides {
>       "hashicorp.com/edu/hashicups-pf" = ""
>   }
> 
>   # For all other providers, install them directly from their origin provider
>   # registries as normal. If you omit this, Terraform will _only_ use
>   # the dev_overrides block, and so no other providers will be available.
>   direct {}
> }
> ``` [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#008802b8-506a-4d73-b1ff-7cf9dd4950b6) 

> Create an `examples/provider-install-verification` directory, which will contain a terraform configuration to verify local provider installation, and navigate to it.
> 
> ```shell
> $ mkdir examples/provider-install-verification &amp;&amp; cd "$_"
> 
> ```
> 
> Create a `main.tf` file with the following.
> 
> examples/provider-install-verification/main.tf
> 
> ```nginx
> terraform {
>   required_providers {
>     hashicups = {
>       source = "hashicorp.com/edu/hashicups-pf"
>     }
>   }
> }
> 
> provider "hashicups" {}
> 
> data "hashicups_coffees" "example" {}
> ``` [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#81dcf6c2-16ec-44c9-8ee8-05bb435ca30a) 

> The `main.tf` Terraform configuration file in this directory uses a "hashicups\_coffees" data source that the provider does not yet support. You will implement this data source in a future tutorial.
> 
> Running a Terraform plan will report the provider override, as well as an error about the missing data source. Even though there was an error, this verifies that Terraform was able to successfully start the locally installed provider and interact with it in your development environment. [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#91562398-1fae-49d8-a574-e583caf1522b) 

> Run a Terraform plan with the non-existent data source. Terraform will respond with the missing data source error.
> 
> ```sql
> $ terraform plan
> ╷
> │ Warning: Provider development overrides are in effect
> │
> │ The following provider development overrides are set in the CLI
> │ configuration:
> │  - hashicorp.com/edu/hashicups-pf in /Users//go/bin
> │
> │ The behavior may therefore not match any released version of the provider and
> │ applying changes may cause the state to become incompatible with published
> │ releases.
> ╵
> ╷
> │ Error: Invalid data source
> │
> │   on main.tf line 11, in data "hashicups_coffees" "example":
> │   11: data "hashicups_coffees" "example" {}
> │
> │ The provider hashicorp.com/edu/hashicups-pf does not support data source
> │ "hashicups_coffees".
> ╵
> ``` [⤴️](https://omnivore.app/me/implement-a-provider-with-the-terraform-plugin-framework-terrafo-188b822ee8c#de5b4ae9-e93a-45fc-a14c-a1d7845a1c2c) 


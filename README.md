# presto-consul-connect

This plugin allows Prestosql coordinator / workers to participate in a Consul Connect Service Mesh by leveraging Native Integration:

https://www.consul.io/docs/connect/native

The rationale here is that Presto's internal distributed architecture makes it hard to go with a sidecar approach, especially when one is looking to extend the zero-trust network for internal communication as well

The plugin is an extension of Prestosql's pluggable Certificate Authenticator backend, available from release 334:

https://prestosql.io/docs/current/develop/certificate-authenticator.html

## compiling sources

- clone repo
- mvn clean install

## deploying plugin

https://prestosql.io/docs/current/develop/spi-overview.html#deploying-a-custom-plugin

## configuration

presto-consul-connect expects the following parameters to be present either in the plugin's config file or as environment variables:

- certificate-authenticator.name=consulconnect
- consul.service=<service_name>
- consul.addr=<consul_address>
- consul.token=<consul_token>

## testing with Nomad job -WIP

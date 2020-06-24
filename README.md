# presto-consul-connect

This plugin allows Prestosql coordinator / workers to participate in a Consul Connect Service Mesh by leveraging Native Integration:

https://www.consul.io/docs/connect/native

The rationale here is that Presto's internal distributed architecture makes it hard to go with a sidecar approach, especially when one is looking to extend the zero-trust network for internal communication as well

The plugin is an extension of Prestosql's pluggable Certificate Authenticator backend, available from release 334:

https://prestosql.io/docs/current/develop/certificate-authenticator.html

## compiling sources

- clone repo
- mvn clean install

## downloading latest release

The latest release can be downloaded from Maven central repo:

https://oss.sonatype.org/#nexus-search;quick~presto-consul-connect

## deploying plugin

https://prestosql.io/docs/current/develop/spi-overview.html#deploying-a-custom-plugin

## configuration

presto-consul-connect expects the following parameters to be present either in the plugin's config file or as environment variables:

- certificate-authenticator.name=consulconnect
- consul.service=<service_name>
- consul.addr=<consul_address>
- consul.token=<consul_token>

## testing 

Instructions on using the official docker image:

https://github.com/prestosql/presto/tree/master/docker

For the plugin to work with this image, the following steps are required:

- create a java keystore based on Consul's certification chain and leaf certificate for Presto; all the required certs can be obtained using Consul's HTTP API (https://www.consul.io/api/agent/connect) or Consul template
- make the keystore available to the container (it must be referenced by config.properties file in an upcoming step)
- copy the jar with dependencies to: /usr/lib/presto/plugin/consulconnect (within the container)
- add a certificate-authenticator.properties file to /lib/presto/default/etc (with the properties mentioned in the previous section)
- configure Presto to use SSL for Presto external / internal communication (https://prestosql.io/docs/current/security/server.html)

## special considerations

- in a multi-node setup, each service associated with Presto (coordinator, workers) can request its own leaf certificate from Consul, then each JKS has a very narrow, focused scope (useful for multi-cloud, multi-datacenter scenarios)
- leaf certificates should be short-lived, so then the JKSs need to be kept updated; an approach to achieve this is by delegating to a certificate handler, which can run out of process either as a system service or as a sidecar container
- hot reloading of SSL certificates is quite challenging in Java; with an application like Presto which is mostly stateless, it might be more practical to just signal the node to gracefully shutdown & restart when the JKS has been updated (https://prestosql.io/docs/current/release/release-0.128.html#graceful-shutdown)

## example

- the following is a ready-made example which runs on Nomad:

    [example](src/main/resources/example/README.md)
# Drogue IoT – OPC UA server for the Doppelgänger Twin state

This is a standard Quarkus application, so the following sections only describe to most important aspects of running
or building this application. All features that Quarkus provides do still apply.

## Running

Either run this as a standard Java application, or using the native binary. The easiest way is to run this using a
container, for example:

```shell
podman run --rm -ti -p 4840:4840 ghcr.io/drogue-iot/drogue-doppelgaenger-opcua-server:latest
```

However, you will need to provide some configuration. The simplest way is to create a `.env` file, and reference
it during the startup:

```shell
podman run --rm -ti -p 4840:4840 --env-file=.env ghcr.io/drogue-iot/drogue-doppelgaenger-opcua-server:latest
```

If you want to run a locally built image, use:

```shell
podman run --rm -ti -p 4840:4840 --env-file=.env drogue-doppelgaenger-opcua-server
```

## Configuration

The application cannot run without a configuration, as it needs to know the Doppelgaenger backend to use. You can
provide the configuration in any way you can [configure a Quarkus application](https://quarkus.io/guides/config).

| Property                                                                                                                                                                                                                                                                                                                          | Type     | Default   |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-----------|
| `drogue.doppelgaenger.api` <br/> The base URL to the Doppelgaenger API<br/>Environment variable: `DROGUE_DOPPELGAENGER_API`                                                                                                                                                                                                       | URL      |           |
| `drogue.doppelgaenger.application` <br/> The name of the application, in case of a multi-instance installation.<br/>Environment variable: `DROGUE_DOPPELGAENGER_APPLICATION`                                                                                                                                                      | String   | `default` |
| `quarkus.oidc-client.auth-server-url` <br/> The URL to the OAuth2 instance used by the Doppelgaeanger instance.<br/>This instance is used in to acquire access tokens for the API.<br/>In case of Keycloak, this is most likely `https://server/realms/<realm>` <br/> Environment variable: `QUARKUS_OIDC_CLIENT_AUTH_SERVER_URL` | URL      |           |
| `quarkus.oidc-client.client-id` <br/> The OAuth2 Client ID<br/>Environment variable: `QUARKUS_OIDC_CLIENT_CLIENT_ID`                                                                                                                                                                                                              | String   |           |
| `quarkus.odic-client.credentials.secret` <br/> The secret for the Client ID<br/>Environment variable: `QUARKUS_OIDC_CLIENT_CREDENTIALS_SECRET`                                                                                                                                                                                    | String   |           |

Additionally, there are some configuration options for the OPC UA server:

| Property                                                                                                                                                                                                                                                                                                                                     | Type          | Default     |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|-------------|
| `drogue.doppelgaenger.opcua.bind-address` <br/> The address to bind to. <br/> Environment variable: `DROGUE_DOPPELGAENGER_OPCUA_BIND_ADDRESS`                                                                                                                                                                                                | `String`      | `localhost` |
| `drogue.doppelgaenger.opcua.bind-port` <br/> The port to bind to. <br/> Environment variable: `DROGUE_DOPPELGAENGER_OPCUA_BIND_PORT`                                                                                                                                                                                                         | `int`         | `4840`      |
| `drogue.doppelgaenger.opcua.hostnames` <br/> The hostnames to announce. By default, it will try to auto-detect, based on the local interfaces and DNS. However, when running inside a container, you might need to override this, with an externally resolvable hostname. <br/> Environment variable: `DROGUE_DOPPELGAENGER_OPCUA_HOSTNAMES` | `Set<String>` | *empty*     |
| `drogue.doppelgaenger.opcua.enable-anonymous` <br/> Enable anonymous access. <br/> Environment variable: `DROGUE_DOPPELGAENGER_OPCUA_ENABLE_ANONYMOUS`                                                                                                                                                                                       | `boolean`     | `false`     |
| `drogue.doppelgaenger.opcua.users.<name>` <br/> Add user/password based access. Multiple entries can be set, assigned value is the password.<br/> Environment variable: `DROGUE_DOPPELGAENGER_OPCUA_USERS_<user>`                                                                                                                            | `boolean`     | `false`     |

A minimum `.env` file is:

```shell
DROGUE_DOPPELGAENGER_API=https://doppelgaenger-api

QUARKUS_OIDC_CLIENT_AUTH_SERVER_URL=https://keycloak/realms/doppelgaenger
QUARKUS_OIDC_CLIENT_CLIENT_ID=services
QUARKUS_OIDC_CLIENT_CREDENTIALS_SECRET=services-client-secret
```

## Developing

You can start the application in developer mode using:

```shell
mvn compile quarkus:dev
```

## Building

To build a new image, run:

```shell
mvn package
podman build . -f src/main/docker/Dockerfile.jvm -t drogue-doppelgaenger-opcua-server
```

To build the native image, run:

```shell
mvn package -Pnative
podman build . -f src/main/docker/Dockerfile.native
```

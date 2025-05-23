# Structure

The configuration must be set in multiple YAML files located in `conf/` folder from the current working directory.

You can create multiple files containing different part of the configuration. A global merge will be done across all data in all files.

Moreover, the configuration files will be watched for modifications.

You can see a full example in the [Example section](./example.md)

## Main structure

| Key            | Type                                                      | Required | Default | Description                                                                                                         |
| -------------- | --------------------------------------------------------- | -------- | ------- | ------------------------------------------------------------------------------------------------------------------- |
| log            | [LogConfiguration](#logconfiguration)                     | No       | None    | Log configurations                                                                                                  |
| server         | [ServerConfiguration](#serverconfiguration)               | No       | None    | Server configurations                                                                                               |
| internalServer | [ServerConfiguration](#serverconfiguration)               | No       | None    | Internal Server configurations                                                                                      |
| template       | [TemplateConfiguration](#templateconfiguration)           | No       | None    | Template configurations                                                                                             |
| targets        | Map[String][targetconfiguration](#targetconfiguration)    | No       | None    | Targets configuration. Map key will be considered as the target name. (This will used in urls and list of targets.) |
| authProviders  | [AuthProvidersConfiguration](#authprovidersconfiguration) | No       | None    | Authentication providers configuration                                                                              |
| listTargets    | [ListTargetsConfiguration](#listtargetsconfiguration)     | No       | None    | List targets feature configuration                                                                                  |
| metrics        | [MetricsConfiguration](#metricsconfiguration)             | No       | None    | Metrics configurations                                                                                              |

## MetricsConfiguration

| Key               | Type    | Required | Default | Description                              |
| ----------------- | ------- | -------- | ------- | ---------------------------------------- |
| disableRouterPath | Boolean | No       | `false` | Disable router path exported in metrics. |

## LogConfiguration

| Key      | Type   | Required | Default | Description                                         |
| -------- | ------ | -------- | ------- | --------------------------------------------------- |
| level    | String | No       | `info`  | Log level                                           |
| format   | String | No       | `json`  | Log format (available values are: `json` or `text`) |
| filePath | String | No       | `""`    | Log file path                                       |

## ServerConfiguration

| Key        | Type                                    | Required | Default | Description                                               |
| ---------- | --------------------------------------- | -------- | ------- | --------------------------------------------------------- |
| listenAddr | String                                  | No       | `""`    | Listen Address (Important: Cannot be hot reloaded)        |
| port       | Integer                                 | No       | `8080`  | Listening Port (Important: Cannot be hot reloaded)        |
| cors       | [ServerCorsConfig](#servercorsconfig)   | No       | None    | CORS configuration                                        |
| cache      | [ServerCacheConfig](#servercacheconfig) | No       | None    | Cache configuration                                       |
| ssl        | [ServerSSLConfig](#serversslconfig)     | No       | None    | SSL/TLS configuration (Important: Cannot be hot reloaded) |

## ServerTimeoutsConfig

| Key               | Type   | Required | Default                             | Description                |
| ----------------- | ------ | -------- | ----------------------------------- | -------------------------- |
| readTimeout       | string | No       | `""`                                | Server read timeout        |
| readHeaderTimeout | string | No       | `"60s"` (to avoid Slowloris attack) | Server read header timeout |
| writeTimeout      | string | No       | `""`                                | Server write timeout       |
| idleTimeout       | string | No       | `""`                                | Server idle timeout        |

## ServerCompressConfig

| Key     | Type     | Required | Default                                                                                                                                                                                       | Description                                |
| ------- | -------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| enabled | Boolean  | No       | `true`                                                                                                                                                                                        |  Is the compression enabled ?              |
| level   | Integer  | No       | `5`                                                                                                                                                                                           | The level of GZip compression              |
| types   | [String] | No       | `["text/html","text/css","text/plain","text/javascript","application/javascript","application/x-javascript","application/json","application/atom+xml","application/rss+xml","image/svg+xml"]` | The content type list compressed in output |

## ServerCacheConfig

| Key            | Type    | Required | Default | Description                             |
| -------------- | ------- | -------- | ------- | --------------------------------------- |
| noCacheEnabled | Boolean | false    | `false` | Force no cache headers on all responses |
| expires        | String  | false    | `""`    | `Expires` header value                  |
| cacheControl   | String  | false    | `""`    | `Cache-Control` header value            |
| pragma         | String  | false    | `""`    | `Pragma` header value                   |
| xAccelExpires  | String  | false    | `""`    | `X-Accel-Expires` header value          |

See more information [here](../feature-guide/cache-control.md).

## ServerCorsConfig

This feature is powered by [go-chi/cors](https://github.com/go-chi/cors). You can read more documentation about all field there.

| Key                | Type     | Required | Default                                                                        | Description                                                       |
| ------------------ | -------- | -------- | ------------------------------------------------------------------------------ | ----------------------------------------------------------------- |
| enabled            | Boolean  | No       | `false`                                                                        | Is CORS support enabled ?                                         |
| allowAll           | Boolean  | No       | `false`                                                                        | Allow all CORS requests with all origins, all HTTP methods, etc ? |
| allowOrigins       | [String] | No       | Allow origins array. Example: https://fake.com. This support stars in origins. |
| allowMethods       | [String] | No       | Allow HTTP Methods                                                             |
| allowHeaders       | [String] | No       | Allow headers                                                                  |
| exposeHeaders      | [String] | No       | Expose headers                                                                 |
| maxAge             | Integer  | No       | Max age. 300 is the maximum value not ignored by any of major browsers.        |
| allowCredentials   | Boolean  | No       | Allow credentials                                                              |
| debug              | Boolean  | No       | Debug mode for [go-chi/cors](https://github.com/go-chi/cors)                   |
| optionsPassthrough | Boolean  | No       | OPTIONS method Passthrough                                                     |

## ServerSSLCertificate

| Key                  | Type                          | Required | Default | Description                                                  |
| -------------------- | ----------------------------- | -------- | ------- | ------------------------------------------------------------ |
| certificate          | String                        | Yes\[1\] | None    | The PEM encoded certificate.                                 |
| certificateUrl       | String                        | Yes\[1\] | None    | The URL of a resource containing the certificate.            |
| certificateUrlConfig | [SSLURLConfig](#sslurlconfig) | No       | None    | Additional URL configuration if certificateUrl is an S3 URL. |
| privateKey           | String                        | Yes\[2\] | None    | The PEM encoded private key.                                 |
| privateKeyUrl        | String                        | Yes\[2\] | None    | The URL of a resource containing the private key.            |
| privateKeyUrlConfig  | [SSLURLConfig](#sslurlconfig) | No       | None    | Additional URL configuration if privateKeyUrl is an S3 URL.  |

Notes:

- \[1\] Exactly one of `certificate` or `certificateUrl` must be specified.
- \[2\] Exactly one of `privateKey` or `privateKeyUrl` must be specified.

Allowed URL types are:

- Local files, in <code>file:///<i>absolute</i>/<i>path</i>/<i>filename</i></code>, <code>file://<i>relative</i>/<i>path</i>/<i>filename</i></code>, <code>/<i>absolute</i>/<i>path</i>/<i>filename</i></code>, or <code><i>relative</i>/<i>path</i>/<i>filename</i></code> form.
- HTTP/HTTPS URLs in <code>https://<i>host</i>[:<i>port</i>]/<i>path</i></code> form.
- AWS S3 URLs in either <code>s3://<i>bucket</i>/<i>key</i></code> or <code>arn:<i>partition</i>:s3:::<i>bucket</i>/<i>key</i></code> form.
- AWS Secrets Manager ARNs in <code>arn:<i>partition</i>:secretsmanager:<i>region</i>:<i>account-id</i>:secret/<i>secret-name</i></code> form.
- AWS Systems Manager parameter ARNs in <code>arn:<i>partition</i>:ssm:<i>region</i>:<i>account-id</i>:parameter/<i>path</i>/<i>name</i></code> form.

## ServerSSLConfig

| Key                 | Type                                              | Required | Default     | Description                                                 |
| ------------------- | ------------------------------------------------- | -------- | ----------- | ----------------------------------------------------------- |
| enabled             | Boolean                                           | No       | `false`     | Whether SSL support should be enabled.                      |
| certificates        | \[[ServerSSLCertificate](#serversslcertificate)\] | No       | \[\]        | Certificates to serve when connected.                       |
| selfSignedHostnames | \[String\]                                        | No       | \[\]        | List of hostnames to generate self-signed certificates for. |
| minTLSVersion       | String                                            | No       | `"TLSv1.2"` | The minimum TLS version to allow when a client connects.    |
| maxTLSVersion       | String                                            | No       | None        | The maximum TLS version to allow when a client connects.    |
| cipherSuites        | \[String\]                                        | No       | See below   | The TLS ciphers to enable.                                  |

The values for `cipherSuites` are the constant names in the Go [crypto/tls](https://pkg.go.dev/crypto/tls#pkg-constants) package,
starting with `TLS_`. The default ciphers are the recommended cipher suites from [ciphersuite.info](https://ciphersuite.info/cs/?security=recommended) supported by Go:

- `TLS_AES_128_GCM_SHA256`
- `TLS_AES_256_GCM_SHA384`
- `TLS_CHACHA20_POLY1305_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256`

## SSLURLConfig

This is a subset/modification of the configuration available from [BucketConfiguration](#bucketconfiguration).

| Key            | Type                                                            | Required | Default     | Description                                                                     |
| -------------- | --------------------------------------------------------------- | -------- | ----------- | ------------------------------------------------------------------------------- |
| httpTimeout    | String (duration)                                               | No       | None        | Timeout for HTTP connections (including AWS calls) to get SSL certificate/keys. |
| awsRegion      | String                                                          | No       | `us-east-1` | AWS region for S3/SSM/Secrets Manager-stored documents.                         |
| awsEndpoint    | String                                                          | No       | None        | Custom endpoint for S3/SSM/Secrets Manager API calls.                           |
| awsCredentials | [BucketCredentialConfiguration](#bucketcredentialconfiguration) | No       | None        | Credentials to access AWS-based documents                                       |
| awsDisableSSL  | Boolean                                                         | No       | `false`     | Disable SSL for AWS API calls. This does not affect `https` URLs.               |

## TemplateConfiguration

<!-- prettier-ignore-start -->
!!! Warning
    Override headers will remove the default value containing the `Content-Type` header. Why ? Because it was though that it was better to know why it is override and not have magical values coming from nowhere.
<!-- prettier-ignore-end -->

| Key                 | Type                                                    | Required | Default                                                                                                                                                             | Description                                                                                           |
| ------------------- | ------------------------------------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| helpers             | [String]                                                | No       | `[templates/_helpers.tpl]`                                                                                                                                          | Template Golang helpers                                                                               |
| targetList          | [TemplateConfigurationItem](#templateconfigurationitem) | No       | `targetList: { path: "templates/target-list.tpl", headers: { "Content-Type": "{{ template \"main.headers.contentType\" . }}" }, status: "200" }`                    | Target list template configuration. More information [here](../feature-guide/templates.md).           |
| folderList          | [TemplateConfigurationItem](#templateconfigurationitem) | No       | `folderList: { path: "templates/folder-list.tpl", headers: { "Content-Type": "{{ template \"main.headers.contentType\" . }}" }, status: "200" }`                    | Folder list template configuration. More information [here](../feature-guide/templates.md).           |
| notFoundError       | [TemplateConfigurationItem](#templateconfigurationitem) | No       | `notFoundError: { path: "templates/not-found-error.tpl", headers: { "Content-Type": "{{ template \"main.headers.contentType\" . }}" }, status: "404" }`             | Not found template configuration. More information [here](../feature-guide/templates.md).             |
| unauthorizedError   | [TemplateConfigurationItem](#templateconfigurationitem) | No       | `unauthorizedError: { path: "templates/unauthorized-error.tpl", headers: { "Content-Type": "{{ template \"main.headers.contentType\" . }}" }, status: "401" }`      | Unauthorized template configuration. More information [here](../feature-guide/templates.md).          |
| forbiddenError      | [TemplateConfigurationItem](#templateconfigurationitem) | No       | `forbiddenError: { path: "templates/forbidden-error.tpl", headers: { "Content-Type": "{{ template \"main.headers.contentType\" . }}" }, status: "403" }`            | Forbidden template configuration. More information [here](../feature-guide/templates.md).             |
| badRequestError     | [TemplateConfigurationItem](#templateconfigurationitem) | No       | `badRequestError: { path: "templates/bad-request-error.tpl", headers: { "Content-Type": "{{ template \"main.headers.contentType\" . }}" }, status: "400" }`         | Bad Request template configuration. More information [here](../feature-guide/templates.md).           |
| internalServerError | [TemplateConfigurationItem](#templateconfigurationitem) | No       | `internalServerError: { path: "templates/internal-server-error.tpl", headers: { "Content-Type": "{{ template \"main.headers.contentType\" . }}" }, status: "500" }` | Internal server error template configuration. More information [here](../feature-guide/templates.md). |
| put                 | [TemplateConfigurationItem](#templateconfigurationitem) | No       | `put: { path: "templates/put.tpl", headers: {}, status: "204" }`                                                                                                    | PUT response template configuration. More information [here](../feature-guide/templates.md).          |
| delete              | [TemplateConfigurationItem](#templateconfigurationitem) | No       | `delete: { path: "templates/put.tpl", headers: {}, status: "204" }`                                                                                                 | DELETE response template configuration. More information [here](../feature-guide/templates.md).       |

## TemplateConfigurationItem

| Key     | Type              | Required | Default | Description                                                                                                                                                                                                               |
| ------- | ----------------- | -------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| path    | String            | True     | `""`    | File path to template file                                                                                                                                                                                                |
| headers | Map[String]String | False    | None    | Headers containing templates. Key corresponds to header and value to the template. If templated value is empty, the header won't be added to answer. More information [here](../feature-guide/templates.md#generic-case). |
| status  | String            | False    | `""`    | Status code template. It will be parsed to get an integer.                                                                                                                                                                |

## TargetConfiguration

| Key            | Type                                          | Required | Default            | Description                                                                                                                                                                                                                              |
| -------------- | --------------------------------------------- | -------- | ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| bucket         | [BucketConfiguration](#bucketconfiguration)   | Yes      | None               | Bucket configuration                                                                                                                                                                                                                     |
| resources      | [[Resource]](#resource)                       | No       | None               | Resources declaration for path whitelist or specific authentication on path list. WARNING: Think about all path that you want to protect. At the end of the list, you should add a resource filter for /\* otherwise, it will be public. |
| mount          | [MountConfiguration](#mountconfiguration)     | Yes      | None               | Mount point configuration                                                                                                                                                                                                                |
| actions        | [ActionsConfiguration](#actionsconfiguration) | No       | GET action enabled | Actions allowed on target (GET, PUT or DELETE)                                                                                                                                                                                           |
| keyRewriteList | [[KeyRewrite]](#keyrewrite)                   | No       | None               | Key rewrite list is here to allow rewriting keys before sending request to S3 (See more information [here](../feature-guide/key-rewrite.md))                                                                                             |
| templates      | [TargetTemplateConfig](#targettemplateconfig) | No       | None               | Custom target templates from files on local filesystem or in bucket                                                                                                                                                                      |

## KeyRewrite

See more information [here](../feature-guide/key-rewrite.md).

| Key        | Type   | Required | Default | Description                                             |
| ---------- | ------ | -------- | ------- | ------------------------------------------------------- |
| source     | String | Required | None    | Source regexp matcher with golang group naming support. |
| targetType | String | No       | `REGEX` | Possible values are `REGEX` or `TEMPLATE`.              |
| target     | String | Required | None    | Target template for new key send to S3.                 |

## TargetTemplateConfig

| Key                 | Type                                                  | Required | Default | Description                                                                                                |
| ------------------- | ----------------------------------------------------- | -------- | ------- | ---------------------------------------------------------------------------------------------------------- |
| helpers             | [[TargetHelperConfigItem](#targethelperconfigitem)]   | No       | None    | Helpers list custom template declarations.                                                                 |
| folderList          | [TargetTemplateConfigItem](#targettemplateconfigitem) | No       | None    | Folder list custom template declaration. More information [here](../feature-guide/templates.md).           |
| notFoundError       | [TargetTemplateConfigItem](#targettemplateconfigitem) | No       | None    | Not Found custom template declaration. More information [here](../feature-guide/templates.md).             |
| internalServerError | [TargetTemplateConfigItem](#targettemplateconfigitem) | No       | None    | Internal server error custom template declaration. More information [here](../feature-guide/templates.md). |
| forbiddenError      | [TargetTemplateConfigItem](#targettemplateconfigitem) | No       | None    | Forbidden custom template declaration. More information [here](../feature-guide/templates.md).             |
| unauthorizedError   | [TargetTemplateConfigItem](#targettemplateconfigitem) | No       | None    | Unauthorized custom template declaration. More information [here](../feature-guide/templates.md).          |
| badRequestError     | [TargetTemplateConfigItem](#targettemplateconfigitem) | No       | None    | Bad Request custom template declaration. More information [here](../feature-guide/templates.md).           |
| put                 | [TargetTemplateConfigItem](#targettemplateconfigitem) | No       | None    | PUT custom template declaration. More information [here](../feature-guide/templates.md).                   |
| delete              | [TargetTemplateConfigItem](#targettemplateconfigitem) | No       | None    | DELETE custom template declaration. More information [here](../feature-guide/templates.md).                |

## TargetHelperConfigItem

| Key      | Type    | Required | Default | Description                                     |
| -------- | ------- | -------- | ------- | ----------------------------------------------- |
| inBucket | Boolean | No       | `false` | Is the file in bucket or on local file system ? |
| path     | String  | Yes      | None    | Path for template file                          |

## TargetTemplateConfigItem

| Key      | Type              | Required | Default                                                                                     | Description                                                                                                                                                                                                               |
| -------- | ----------------- | -------- | ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| inBucket | Boolean           | No       | `false`                                                                                     | Is the file in bucket or on local file system ?                                                                                                                                                                           |
| path     | String            | Yes      | None                                                                                        | Path for template file                                                                                                                                                                                                    |
| headers  | Map[String]String | False    | This will be set to corresponding [TemplateConfiguration](#templateconfiguration) if empty. | Headers containing templates. Key corresponds to header and value to the template. If templated value is empty, the header won't be added to answer. More information [here](../feature-guide/templates.md#generic-case). |
| status   | String            | Yes      | None                                                                                        | Status code template. It will be parsed to get an integer.                                                                                                                                                                |

## ActionsConfiguration

| Key    | Type                                                    | Required | Default | Description                                        |
| ------ | ------------------------------------------------------- | -------- | ------- | -------------------------------------------------- |
| HEAD   | [HeadActionConfiguration](#headactionconfiguration)     | No       | None    | Action configuration for HEAD requests on target   |
| GET    | [GetActionConfiguration](#getactionconfiguration)       | No       | None    | Action configuration for GET requests on target    |
| PUT    | [PutActionConfiguration](#putactionconfiguration)       | No       | None    | Action configuration for PUT requests on target    |
| DELETE | [DeleteActionConfiguration](#deleteactionconfiguration) | No       | None    | Action configuration for DELETE requests on target |

## HeadActionConfiguration

| Key     | Type                                                              | Required | Default | Description                     |
| ------- | ----------------------------------------------------------------- | -------- | ------- | ------------------------------- |
| enabled | Boolean                                                           | No       | `false` | Will allow HEAD requests        |
| config  | [HeadActionConfigConfiguration](#deleteactionconfigconfiguration) | No       | None    | Configuration for HEAD requests |

## HeadActionConfigConfiguration

| Key      | Type                                            | Required | Default | Description                                                          |
| -------- | ----------------------------------------------- | -------- | ------- | -------------------------------------------------------------------- |
| webhooks | [[WebhookConfiguration](#webhookconfiguration)] | No       | `nil`   | Webhooks configuration list to call when a HEAD request is performed |

## GetActionConfiguration

| Key     | Type                                                          | Required | Default | Description                    |
| ------- | ------------------------------------------------------------- | -------- | ------- | ------------------------------ |
| enabled | Boolean                                                       | No       | `false` | Will allow GET requests        |
| config  | [GetActionConfigConfiguration](#getactionconfigconfiguration) | No       | None    | Configuration for GET requests |

## GetActionConfigConfiguration

| Key                                      | Type                                                                                                                                         | Required | Default  | Description                                                                                                                                                                                                                                                                        |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| redirectWithTrailingSlashForNotFoundFile | Boolean                                                                                                                                      | No       | `false`  | This option allow to do a redirect with a trailing slash when a GET request on a file (not a folder) encountered a 404 not found.                                                                                                                                                  |
| indexDocument                            | String                                                                                                                                       | No       | `""`     | The index document name. If this document is found, get it instead of list folder. Example: `index.html`                                                                                                                                                                           |
| streamedFileHeaders                      | Map[String]String                                                                                                                            | No       | `nil`    |  Headers containing templates that will be added to streamed files in this target. Key corresponds to header and value to the template. If templated value is empty, the header won't be added to answer. More information [here](../feature-guide/templates.md#stream-file-case). |
| redirectToSignedUrl                      | Boolean                                                                                                                                      | No       | `false`  | Instead of streaming the file through S3-Proxy application, it will redirect to a S3 signed URL to perform the actual download.                                                                                                                                                    |
| signedUrlExpiration                      | String                                                                                                                                       | No       | `15m`    | This will allow to set an expiration time on generated signed URL.                                                                                                                                                                                                                 |
| disableListing                           | That will disable the listing action. That will display an empty list or you should change the folder list template (general or per target). | No       | `false`  |
| webhooks                                 | [[WebhookConfiguration](#webhookconfiguration)]                                                                                              | No       | `nil`    | Webhooks configuration list to call when a GET request is performed                                                                                                                                                                                                                |

## PutActionConfiguration

| Key     | Type                                                          | Required | Default | Description                    |
| ------- | ------------------------------------------------------------- | -------- | ------- | ------------------------------ |
| enabled | Boolean                                                       | No       | `false` | Will allow PUT requests        |
| config  | [PutActionConfigConfiguration](#putactionconfigconfiguration) | No       | None    | Configuration for PUT requests |

## PutActionConfigConfiguration

| Key            | Type                                                                                      | Required | Default | Description                                                                                                                                                                                                                                                                                                                                           |
| -------------- | ----------------------------------------------------------------------------------------- | -------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| metadata       | Map[String]String                                                                         | No       | None    | Metadata key/values that will be put on S3 objects. Map Values can be templated. Empty values will be flushed. See [here](../feature-guide/templates.md#put-metadata)                                                                                                                                                                                 |
| systemMetadata | [PutActionConfigSystemMetadataConfiguration](#putactionconfigsystemmetadataconfiguration) | No       | `nil`   | This allow to put system metadata values to uploaded objects. Value can be templated. Empty values will be flushed. See [here](../feature-guide/templates.md#put-metadata)                                                                                                                                                                            |
| storageClass   | String                                                                                    | No       | `""`    | Storage class that will be used for uploaded objects. See storage class here: [https://docs.aws.amazon.com/AmazonS3/latest/dev/storage-class-intro.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/storage-class-intro.html). Value can be templated. Empty values will be flushed. See [here](../feature-guide/templates.md#put-storage-class) |
| allowOverride  | Boolean                                                                                   | No       | `false` | Will allow override objects if enabled                                                                                                                                                                                                                                                                                                                |
| cannedACL      | String                                                                                    | No       | `nil`   | Canned ACL put on each file uploaded. See official values here [https://docs.aws.amazon.com/AmazonS3/latest/userguide/acl-overview.html#canned-acl](https://docs.aws.amazon.com/AmazonS3/latest/userguide/acl-overview.html#canned-acl).                                                                                                              |
| webhooks       | [[WebhookConfiguration](#webhookconfiguration)]                                           | No       | `nil`   | Webhooks configuration list to call when a PUT request is performed                                                                                                                                                                                                                                                                                   |

## PutActionConfigSystemMetadataConfiguration

| Key                | Type   | Required | Default                                                                                                                                                                       | Description |
| ------------------ | ------ | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| cacheControl       | String | `""`     | Cache-Control value. Value can be templated. Empty values will be flushed. See [here](../feature-guide/templates.md#put-metadata)                                             |
| contentDisposition | String | `""`     | Content-Disposition value. Value can be templated. Empty values will be flushed. See [here](../feature-guide/templates.md#put-metadata)                                       |
| contentEncoding    | String | `""`     | Content-Encoding value. Value can be templated. Empty values will be flushed. See [here](../feature-guide/templates.md#put-metadata)                                          |
| contentLanguage    | String | `""`     | Content-Language value. Value can be templated. Empty values will be flushed. See [here](../feature-guide/templates.md#put-metadata)                                          |
| expires            | String | `""`     | Expires value This must have the RFC3339 date format at the end. Value can be templated. Empty values will be flushed. See [here](../feature-guide/templates.md#put-metadata) |

## DeleteActionConfiguration

| Key     | Type                                                                | Required | Default | Description                       |
| ------- | ------------------------------------------------------------------- | -------- | ------- | --------------------------------- |
| enabled | Boolean                                                             | No       | `false` | Will allow DELETE requests        |
| config  | [DeleteActionConfigConfiguration](#deleteactionconfigconfiguration) | No       | None    | Configuration for DELETE requests |

## DeleteActionConfigConfiguration

| Key      | Type                                            | Required | Default | Description                                                            |
| -------- | ----------------------------------------------- | -------- | ------- | ---------------------------------------------------------------------- |
| webhooks | [[WebhookConfiguration](#webhookconfiguration)] | No       | `nil`   | Webhooks configuration list to call when a DELETE request is performed |

## WebhookConfiguration

You can found more information [here](../feature-guide/webhooks.md) about webhooks and this works in the application.

| Key             | Type                                                           | Required | Default | Description                                                                                     |
| --------------- | -------------------------------------------------------------- | -------- | ------- | ----------------------------------------------------------------------------------------------- |
| method          | String                                                         | Yes      | None    | HTTP Method used for webhook call. Can be `POST`, `PUT`, `DELETE` or `PATCH`                    |
| url             | String                                                         | Yes      | None    | URL to be called                                                                                |
| headers         | Map[String]String                                              | No       | `nil`   | Fixed headers                                                                                   |
| secretHeaders   | Map[String][credentialconfiguration](#credentialconfiguration) | No       | `nil`   | Headers coming from secrets (for credentials for example)                                       |
| retryCount      | Integer                                                        | No       | `0`     | Number of retry in case of error                                                                |
| defaultWaitTime | String                                                         | No       | `""`    | Default wait time to sleep before retrying request. Default is 100 ms (injected by HTTP client) |
| maxWaitTime     | String                                                         | No       | `""`    | Max wait time to sleep before retrying request. Default is 2 seconds (injected by HTTP client)  |

## BucketConfiguration

| Key                       | Type                                                                  | Required | Default     | Description                                                                                                                                                                                                                                                                              |
| ------------------------- | --------------------------------------------------------------------- | -------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name                      | String                                                                | Yes      | None        | Bucket name in S3 provider                                                                                                                                                                                                                                                               |
| prefix                    | String                                                                | No       | None        | Bucket prefix                                                                                                                                                                                                                                                                            |
| region                    | String                                                                | No       | `us-east-1` | Bucket region                                                                                                                                                                                                                                                                            |
| s3Endpoint                | String                                                                | No       | None        | Custom S3 Endpoint for non AWS S3 bucket                                                                                                                                                                                                                                                 |
| credentials               | [BucketCredentialConfiguration](#bucketcredentialconfiguration)       | No       | None        | Credentials to access S3 bucket                                                                                                                                                                                                                                                          |
| disableSSL                | Boolean                                                               | No       | `false`     | Disable SSL connection                                                                                                                                                                                                                                                                   |
| s3ListMaxKeys             | Integer                                                               | No       | `1000`      | This flag will be used for the max pagination list management of files and "folders" in S3. In S3 list requests, the limit is fixed to 1000 items maximum. S3-Proxy will allow to increase this by making multiple requests to S3. Warning: This will increase the memory and CPU usage. |
| requestConfig             | [BucketRequestConfigConfiguration](#bucketrequestconfigconfiguration) | No       | `nil`       | This will allow to customize requests sent to your S3 backend.                                                                                                                                                                                                                           |
| s3MaxUploadParts          | Integer                                                               | No       | `10000`     | MaxUploadParts is the max number of parts which will be uploaded to S3.                                                                                                                                                                                                                  |
| s3UploadPartSize          | Integer                                                               | No       | `5`         | The buffer size (in megabytes) to use when buffering data into chunks and sending them as parts to S3. The minimum allowed part size is 5MB, and if this value is set to zero, the DefaultUploadPartSize value will be used.                                                             |
| s3UploadConcurrency       | Integer                                                               | No       | `5`         | The number of goroutines to spin up in parallel per call to Upload when sending parts. If this is set to zero, the DefaultUploadConcurrency value will be used.                                                                                                                          |
| s3UploadLeavePartsOnError | Boolean                                                               | No       | `false`     | Setting this value to true will cause the SDK to avoid calling AbortMultipartUpload on a failure, leaving all successfully uploaded parts on S3 for manual recovery.                                                                                                                     |

## BucketRequestConfigConfiguration

| Key           | Type              | Required | Default | Description                                                            |
| ------------- | ----------------- | -------- | ------- | ---------------------------------------------------------------------- |
| listHeaders   | Map[string]string | No       | `nil`   | Will allow to customize headers made to S3 provider on List requests   |
| getHeaders    | Map[string]string | No       | `nil`   | Will allow to customize headers made to S3 provider on Get requests    |
| putHeaders    | Map[string]string | No       | `nil`   | Will allow to customize headers made to S3 provider on Put requests    |
| deleteHeaders | Map[string]string | No       | `nil`   | Will allow to customize headers made to S3 provider on Delete requests |

## BucketCredentialConfiguration

| Key       | Type                                                | Required | Default | Description          |
| --------- | --------------------------------------------------- | -------- | ------- | -------------------- |
| accessKey | [CredentialConfiguration](#credentialconfiguration) | No       | None    | S3 Access Key ID     |
| secretKey | [CredentialConfiguration](#credentialconfiguration) | No       | None    | S3 Secret Access Key |

## CredentialConfiguration

| Key   | Type   | Required                           | Default | Description                                                                     |
| ----- | ------ | ---------------------------------- | ------- | ------------------------------------------------------------------------------- |
| path  | String | Only if env and value are not set  | None    | File path contains credential in (Values loaded will be cleaned from new lines) |
| env   | String | Only if path and value are not set | None    | Environment variable name to use to load credential                             |
| value | String | Only if path and env are not set   | None    | Credential value directly (Not recommended)                                     |

## AuthProvidersConfiguration

| Key    | Type                                                           | Required | Default | Description                                        |
| ------ | -------------------------------------------------------------- | -------- | ------- | -------------------------------------------------- |
| basic  | [map[string]BasicAuthConfiguration](#basicauthconfiguration)   | No       | None    | Basic Auth configuration and key as provider name  |
| oidc   | [map[string]OIDCAuthConfiguration](#oidcauthconfiguration)     | No       | None    | OIDC Auth configuration and key as provider name   |
| header | [map[string]HeaderAuthConfiguration](#headerauthconfiguration) | No       | None    | Header Auth configuration and key as provider name |

## HeaderAuthConfiguration

This authentication method should be used only with a software like [Oauth2-proxy](https://github.com/oauth2-proxy/oauth2-proxy) or an authentication gateway that put headers with user information inside.

<!-- prettier-ignore-start -->
!!! Warning
    S3-proxy won't validate headers value or anything else. It will take values as they are coming.
<!-- prettier-ignore-end -->

| Key            | Type     | Required | Default | Description                                                                  |
| -------------- | -------- | -------- | ------- | ---------------------------------------------------------------------------- |
| usernameHeader | String   | Yes      | None    | Username header                                                              |
| emailHeader    | String   | Yes      | None    | Email header                                                                 |
| groupsHeader   | [String] | No       | `""`    | Groups header. Note: Value must be a list of groups separated by comas (`,`) |

## OIDCAuthConfiguration

| Key           | Type                                                | Required | Default                          | Description                                                                                                                                                                                                |
| ------------- | --------------------------------------------------- | -------- | -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| clientID      | String                                              | Yes      | None                             | Client ID                                                                                                                                                                                                  |
| clientSecret  | [CredentialConfiguration](#credentialconfiguration) | No       | None                             | Client Secret                                                                                                                                                                                              |
| issuerUrl     | String                                              | Yes      | None                             | Issuer URL (example: https://fake.com/realm/fake-realm                                                                                                                                                     |
| redirectUrl   | String                                              | No       | `""`                             | Redirect URL (this is the service url). Without this being set, the redirect url will be calculated from input host automatically by S3-Proxy                                                              |
| scopes        | [String]                                            | No       | `["openid", "profile", "email"]` | Scopes                                                                                                                                                                                                     |
| state         | String                                              | Yes      | None                             | Random string to have a secure connection with oidc provider                                                                                                                                               |
| groupClaim    | String                                              | No       | `groups`                         | Groups claim path in token (`groups` must be a list of strings containing user groups)                                                                                                                     |
| emailVerified | Boolean                                             | No       | `false`                          | Check that user email is verified in user token (field `email_verified`)                                                                                                                                   |
| cookieName    | String                                              | No       | `oidc`                           | Cookie generated name                                                                                                                                                                                      |
| cookieSecure  | Boolean                                             | No       | `false`                          | Is the cookie generated secure ?                                                                                                                                                                           |
| cookieDomains | [String]                                            | No       | `nil`                            | Cookie domains affected to generated cookie. If request host is matching one of the cookie domains defined, generated cookie will use the matching domain, otherwise, the domain will be the request host. |
| loginPath     | String                                              | No       | `""`                             | Override login path for authentication. If not defined, `/auth/PROVIDER_NAME` will be used                                                                                                                 |
| callbackPath  | String                                              | No       | `""`                             | Override callback path for authentication callback. If not defined,`/auth/PROVIDER_NAME/callback` will be used                                                                                             |

## BasicAuthConfiguration

| Key   | Type   | Required | Default | Description      |
| ----- | ------ | -------- | ------- | ---------------- |
| realm | String | Yes      | None    | Basic Auth Realm |

## Resource

| Key       | Type                                      | Required                                    | Default | Description                                                          |
| --------- | ----------------------------------------- | ------------------------------------------- | ------- | -------------------------------------------------------------------- |
| path      | String                                    | Yes                                         | None    | Path or matching path (e.g.: `/*`)                                   |
| provider  | String                                    | Yes                                         | None    | Provider key reference                                               |
| methods   | [String]                                  | No                                          | `[GET]` | HTTP methods allowed (Allowed values `HEAD`, `GET`, `PUT`, `DELETE`) |
| whiteList | Boolean                                   | Required without oidc or basic              | None    | Is this path in white list ? E.g.: No authentication                 |
| basic     | [ResourceBasic](#resourcebasic)           | Required without whitelist, oidc or header  | None    | Basic auth configuration                                             |
| oidc      | [ResourceHeaderOIDC](#resourceheaderoidc) | Required without whitelist, basic or header | None    | OIDC configuration authorization                                     |
| header    | [ResourceHeaderOIDC](#resourceheaderoidc) | Required without whitelist, oidc or basic   | None    | Header configuration authorization                                   |

## ResourceHeaderOIDC

| Key                    | Type                                                                  | Required | Default | Description                                                                                                                                                                                                                                                                                                                                                                                                       |
| ---------------------- | --------------------------------------------------------------------- | -------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| authorizationAccesses  | [[HeaderOIDCAuthorizationAccesses]](#headeroidcauthorizationaccesses) | No       | None    | Authorization accesses matrix by group or email. If not set, authenticated users will be authorized (no group or email validation will be performed if authorizationOPAServer isn't set). This is based on the "OR" principle. Another way to say it is: you are authorized as soon as 1 thing (email or group) is matching. Check the guide [here](../feature-guide/authorization-accesses.md) for more details. |
| authorizationOPAServer | [OPAServerAuthorization](#opaserverauthorization)                     | No       | None    | Authorization through an OPA (Open Policy Agent) server                                                                                                                                                                                                                                                                                                                                                           |

## OPAServerAuthorization

| Key  | Type              | Required | Default | Description                                                                                                          |
| ---- | ----------------- | -------- | ------- | -------------------------------------------------------------------------------------------------------------------- |
| url  | String            | Yes      | None    | URL of the OPA server including the data path (see the dedicated section for [OPA](../feature-guide/opa.md))         |
| tags | Map[String]String | No       | `{}`    | Data that will be added as tags in the OPA input data (see the dedicated section for [OPA](../feature-guide/opa.md)) |

## HeaderOIDCAuthorizationAccesses

| Key       | Type    | Required               | Default | Description                                                                                                                                                                      |
| --------- | ------- | ---------------------- | ------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| group     | String  | Required without email | None    | Group name                                                                                                                                                                       |
| email     | String  | Required without group | None    | Email                                                                                                                                                                            |
| regexp    | Boolean | No                     | `false` | Consider group or email as regexp for matching                                                                                                                                   |
| forbidden | Boolean | No                     | `false` | This will consider anything matching group or email as a forbidden matching (regex enabled or not). This have been done because there isn't way to do a negative match on regex. |

## ResourceBasic

| Key         | Type                                                        | Required | Default | Description                          |
| ----------- | ----------------------------------------------------------- | -------- | ------- | ------------------------------------ |
| credentials | [[BasicAuthUserConfiguration]](#basicauthuserconfiguration) | Yes      | None    | List of authorized user and password |

## BasicAuthUserConfiguration

| Key      | Type                                                | Required | Default | Description   |
| -------- | --------------------------------------------------- | -------- | ------- | ------------- |
| user     | String                                              | Yes      | None    | User name     |
| password | [CredentialConfiguration](#credentialconfiguration) | Yes      | None    | User password |

## MountConfiguration

| Key  | Type     | Required | Default | Description                                                                                                                            |
| ---- | -------- | -------- | ------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| host | String   | No       | `""`    | Host domain requested (eg: localhost:888 or google.fr). Put empty for all domains. Note: Glob patterns for host domains are supported. |
| path | [String] | Yes      | None    | A path list for mounting point                                                                                                         |

## ListTargetsConfiguration

| Key      | Type                                      | Required | Default | Description                                                                 |
| -------- | ----------------------------------------- | -------- | ------- | --------------------------------------------------------------------------- |
| enabled  | Boolean                                   | Yes      | None    | To enable the list targets feature                                          |
| mount    | [MountConfiguration](#mountconfiguration) | Yes      | None    | Mount point configuration                                                   |
| resource | [Resource](#resource)                     | No       | None    | Resources declaration for path whitelist or specific authentication on path |

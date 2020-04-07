# Environments

The clevabit API provides two different base URLs. One is for non-production, development
and testing (Sandbox), whereas the other one must be used for production use only
(Production).

## Sandbox

The Sandbox provides demo data from testing devices, which are not related to any real
world customer. That said, the Sandbox cannot access real customer data.

To login into the Sandbox, use the same application id and application key to be used
against the production environment, however username/email and password are different
and valid only with the sandbox environment.

Apart from that, the sandbox has one additional feature, which is not available in the
production environment. With an _accept_ HTTP header in request set to _application/json_
you can ask the Sandbox version of the API to respond with JSON instead of CBOR objects.
This may make debugging of responses much easier.

Furthermore, the Sandbox has relaxed [Rate Limits](#rate-limits), therefore is suited
for development and debugging uses.

## Production

The Production environment is used for real world requests, being directed towards real
customer data.

<!-- theme: danger -->
> This API environment must not be used for development or debugging.

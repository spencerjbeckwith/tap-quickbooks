# tap-quickbooks

This is a fork of [the original tap-quickbooks](https://github.com/hotgluexyz/tap-quickbooks) to add proper secret management of the Quickbooks refresh token so it will work properly with [meltano](https://meltano.com). By only writing an updated refresh token to a file, the tap would break after 24 hours because it wasn't properly reflected in the environment when it was updated. This version instead reads/writes the refresh_token to AWS Secrets Manager. The configuration is adjusted to reflect this.

## Usage with Meltano

You will want to create a custom plugin using this repository's URL. 

`meltano add extractor --custom tap-quickbooks`

For pip_url, enter `git+https://github.com/spencerjbeckwith/tap-quickbooks.git`. Other options can remain their defaults and you can add your configuration and secrets to meltano.yml after the fact.

Example configuration from meltano.yml, including all necessary config:

```yml
plugins:
  extractors:
  - name: tap-quickbooks
    namespace: tap-quickbooks
    pip_url: git+https://github.com/spencerjbeckwith/tap-quickbooks.git
    executable: tap-quickbooks
    config:
      select_fields_by_default: true
      start_date: '2024-05-01T00:00:00Z'
      realmId: # from quickbooks
      aws_region: us-east-2 # or whatever region your secret is in
      refresh_token_secret: quickbooks_tap_refresh_token # or whatever you've named the secret
      # is_sandbox: true
    # Specifying these settings will load them from your .env
    settings:
      - name: client_id
        sensitive: true
      - name: client_secret
        sensitive: true
      - name: aws_access_key_id
        sensitive: true
      - name: aws_secret_access_key
        sensitive: true
```

Note that your AWS secret must be stored in plaintext, not JSON format.

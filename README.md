# Let's Encrypt TLS Certificte for TrueNAS (Core)

This is a Python script to deploy TLS certificates to a TrueNAS (Core) using the TrueNAS API.  This should ensure that the certificate data is properly stored in the configuration database, and that all appropriate services use this certificate.


## Installation
This script can run on any machine running Python 3 that has network access to your FreeNAS/TrueNAS server, but in most cases it's best to run it directly on the TrueNAS box.

If you're not running this script on your TrueNAS server itself, you'll need to make sure that the Python `requests` module is available (it's there by default in TrueNAS).

### Usage

The relevant configuration takes place in the `deploy_config` file.  You can create this file either by copying `deploy_config.example` from this repository, or directly using your preferred text editor.  Its format is as follows:

``` shell
[deploy]
password = YourReallySecureRootPassword
cert_fqdn = foo.bar.baz
connect_host = baz.bar.foo
verify = false
privkey_path = /some/other/path
fullchain_path = /some/other/other/path
protocol = https://
port = 443
ui_certificate_enabled = false
s3_enabled = false
ftp_enabled = false
webdav_enabled = false
apps_enabled = false
apps_only_matching_san = false
cert_base_name = letsencrypt
```

Everything except `password` (or `api_key`) is optional.

On TrueNAS (Core) 12.0 and up you should use API key authentication instead of password authentication.
[Generate a new API token in the UI](https://www.truenas.com/docs/hub/additional-topics/api/#creating-api-keys) first, then add it as `api_key` to the config, which replaces the `password` field:

``` shell
api_key = 1-DXcZ19sZoZFdGATIidJ8******************************
```

Once you've prepared `deploy_config`, you can run `deploy_freenas.py`.  The intended use is that it would be called by your ACME client after issuing a certificate.  With acme.sh, for example, you'd add `--reloadcmd "/path/to/deploy_freenas.py"` to your command.

There is an optional paramter, `-c` or `--config`, that lets you specify the path to your configuration file. By default the script will try to use `deploy_config` in the script working directoy:

``` shell
/path/to/deploy_freenas.py --config /somewhere/else/deploy_config
```
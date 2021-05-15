# Praeco

![GitHub release](https://img.shields.io/github/release/johnsusek/praeco.svg)
![Docker Pulls](https://img.shields.io/docker/pulls/praecoapp/praeco.svg)
![GitHub stars](https://img.shields.io/github/stars/johnsusek/praeco.svg?style=social&label=Stars)

<img align="left" src="https://user-images.githubusercontent.com/611996/52907999-50fca900-3232-11e9-8aee-40f7dc37ec65.jpg">

**Praeco** is an alerting tool for Elasticsearch – a GUI for [Elastalert 2](https://github.com/jertel/elastalert2), using the [Elastalert API](https://github.com/johnsusek/elastalert-server).

- Interactively build alerts for your Elasticsearch data using a query builder
- Preview results in an interactive chart
- Test your alerts against historical data
- Send notifications to Slack, MS Teams, Email, Telegram, Jira, Line Notify, Mattermost, Command, Gitter, SNS, Zabbix, Twilio, PagerTree, Exotel, GoogleChat, Stomp, VictorOps, ServiceNow, Chatwork, Discord, TheHive, Alerta, Datadog or an HTTP POST endpoint
- Supports the Any, Blacklist, Whitelist, Change, Frequency, Flatline, Spike, Cardinality, New Term, and Metric Aggregation rule types
- View logs of when your alerts check, fire and fail

<br>
<br>

👉 Praeco is a completely free GPLv3 project, in return I only ask that you fill out [this simple survey](https://forms.gle/nAbu1RN2KHnyXX7L8) about how you use it.

##

![](https://user-images.githubusercontent.com/611996/47752071-7c4a9080-dc61-11e8-8ccf-2196f13429b2.png)


## Quickstart

Run the app using Docker compose. Praeco includes everything you need to get started. Just provide it the IP address of your Elasticsearch instance.

```bash
export PRAECO_ELASTICSEARCH=<your elasticsearch ip>
docker-compose up
```

* Don't use 127.0.0.1 for PRAECO_ELASTICSEARCH. See first item under the Troubleshooting section.

* To set up notification settings like API keys edit `rules/BaseRule.config`.

Praeco should now be available on http://127.0.0.1:8080

~~A walkthrough article is available to guide you through creating your first rule.~~


## Upgrading

```
docker pull praecoapp/praeco
docker pull praecoapp/elastalert-server
docker-compose up --force-recreate --build
```

You may need to update your config files when a new version comes out. Please see [UPGRADING.md](https://github.com/johnsusek/praeco/blob/master/UPGRADING.md) for version-specific instructions.

## Configuration

Edit `rules/BaseRule.config`, `config/api.config.json`, `config/elastalert.yaml`, and/or `public/praeco.config.json` for advanced configuration options. See the [api docs](https://github.com/johnsusek/elastalert-server#configuration) and the [example Elastalert 2 config](https://github.com/jertel/elastalert/blob/alt/config.yaml.example) for more information.

Any Elastalert 2 option you put into `rules/BaseRule.config` will be applied to every rule.

The following config settings are available in praeco.config.json:

```
// Link back to your praeco instance, used in Slack alerts
"appUrl": "http://praeco-app-url:8080",

// A recordatus (https://github.com/johnsusek/recordatus) instance for javascript error reporting
"errorLoggerUrl": "",

// Hide these fields when editing rules, if they are already filled in template
"hidePreconfiguredFields": []
```
## DockerHub

[Praeco](https://hub.docker.com/r/praecoapp/praeco)

[ElastAlert Server](https://hub.docker.com/r/praecoapp/elastalert-server)

## FAQ

#### Is it possible to set Percentage Match on the screen?

Not Support

#### Is it possible to set Spike Aggregation on the screen?

Not Support

#### Is it possible to set custom format (timestamp_type, timestamp_format, timestamp_format_expr) on the screen?

Not Support

#### Mulit-elasticsearch instances

Not Support

#### [Third Party Tools] ElastAlert Server & Praeco Helm Chart

[ElastAlert Server Helm Chart](https://github.com/daichi703n/elastalert-helm)<br>
[Praeco Helm Chart](https://github.com/daichi703n/praeco-helm)<br>
[Installing Praeco (ElastAlert GUI) into Kubernetes with Helm](https://en-designetwork.daichi703n.com/entry/2020/02/24/praeco-helm-kubernetes)

#### How do I connect to elasticsearch using SSL?

Edit `config/api.config.json` and set/add `"es_ssl": true`.<br>
option `"es_ca_certs"`, `"es_client_cert"`, `"es_client_key"`.<br>
[configuration](https://github.com/johnsusek/elastalert-server#configuration)

#### How do I connect to elasticsearch with a username and password?

Edit `es_username` and `es_password` in `config/api.config.json` and `config/elastalert.yaml`.

#### How do I serve the praeco UI over https?

The praeco UI is served by an included nginx server (see Dockerfile). Configure it as you would any nginx project by editing the files in `nginx_config`. Then update your docker-compose.yml and add your certificate files (under webapp volumes). Another option is using a reverse proxy.

#### How do I serve the praeco UI under a custom base path, i.e. `http://www.my-domain.com:8080/my-path/`

Uncomment the declaration of the `VUE_APP_BASE_URL` environment variable in `docker-compose.yml` and define the path you want.
```yaml
    environment:
      VUE_APP_BASE_URL: /my-path/
```
Uncomment the rewrite command in `nginx.config/default.conf` and define the same path as in teh environment variable above.
```
rewrite ^/my-path(/.*)$ $1 last;
```
#### How do I change the writeback index?

Edit `config/elastalert.yaml` and `config/api.config.json` and change the writeback_index values.

#### How do I change elastalert 2 options, like SSL, user/pass, etc?

Edit `config/elastalert.yaml` and uncomment the appropriate lines.

#### How do I run this on Windows?

First, install docker and docker-compose.

Then, using powershell, run these commands:

```
$Env:PRAECO_ELASTICSEARCH="1.2.3.4"
docker-compose.exe up
```

Replace 1.2.3.4 with your Elasticsearch IP.

#### Can I import my current elastalert 2 rules into praeco?

Unfortunately this is not a possibility for two reasons. First, praeco only supports a subset of elastalert 2 features, so only certain rules would work. Second, praeco cannot automatically create the query builder ui from an arbitrary elastalert 2 `filter` entry, due to the potential complexity and combinations of filters someone can put in their rule file.

#### Can I export my praeco rules into another elastalert  2 instance?

Yes, the praeco rule files are 100% compatible with other elastalert servers.

## Troubleshooting

#### I am using 127.0.0.1 for PRAECO_ELASTICSEARCH and it isn't working

Praeco, running within a docker container, cannot communicate with your ES bound to localhost. You need to change your ES `network.host` setting
to something different. The value of `_site_` is suggested, that will bind to a local network IP on your machine. Then use that
IP address for PRAECO_ELASTICSEARCH. Here's a working example:

```sh
elasticsearch -E network.host=_site_
export PRAECO_ELASTICSEARCH=192.168.1.145
mkdir -p rules rule_templates
chmod -R 777 rules rule_templates
docker-compose up
```

Replace 192.168.1.145 with the IP address your ES binds to (look for bound_addresses in the elasticsearch launch log).

#### I am getting high CPU usage on some of my rules

When editing a rule, click "WITH OPTIONS" and try using the "Use count query" option. This can dramatically speed up processing time for large amounts of data (tens of thousands of results).

#### I'm not receiving alerts even though I expect them

First of all, try to test your alert with varying time frames and see if that is returning any results.

If the test is returning results, but you are not receiving any alerts, check the error log. There may be a problem with your alerter settings. Make sure you edited rules/BaseRule.config and have correct values in there.

If the test is not returning results, even though you think it should, try reading the [elastalert 2 docs](https://elastalert2.readthedocs.io/en/latest/ruletypes.html#rule-types) for your rule type. Compare the yaml from praeco with the options from the docs to make sure the rule is being created as expected. If praeco is generating the wrong yaml, please file an issue.

#### Failed to establish a new connection: [Errno 111] Connection refused

You will see this error when launching if praeco cannot find elasticsearch at the IP address you specified at \$PRAECO_ELASTICSEARCH. Please make sure you can communicate with this IP address by issuing the following command: `curl http://$PRAECO_ELASTICSEARCH:9200`. If the connection is refused, your machine cannot communicate with Elasticsearch, it may be a networking problem.

#### 404 error in error log for slack webhook

Make sure the channel/username you are trying to post to exists.

#### How to setup TheHive?

Please see https://elastalert2.readthedocs.io/en/latest/ruletypes.html#thehive for how to configure your BaseRule.config file.

- Set hive_connection in BaseRule.config.
- Please set hive_alert_config on the screen.

#### How to setup Slack?

Please see https://elastalert2.readthedocs.io/en/latest/ruletypes.html#slack for how to configure your BaseRule.config file.

Replace slack_webhook_url with the URL of your channel.

```yaml
slack_webhook_url: 'https://hooks.slack.com/services/xxxxxxxxx/xxxxxxxx/xxxxxxxxxxxxxxxxxxx'
```

#### How to setup Telegram?

telegram_room_id can be set on the praeco screen.

Please see https://elastalert2.readthedocs.io/en/latest/ruletypes.html#telegram for how to configure your BaseRule.config file.

Replace telegram_bot_token with the your bot token.

telegram_proxy, telegram_proxy_login and telegram_proxy_pass do not need to be written in BaseRule.config if they do not need to be set.

```yaml
telegram_bot_token: 'xxxxxxxxxx:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'
```

#### How to setup Mattermost? 

Please see https://elastalert2.readthedocs.io/en/latest/ruletypes.html#mattermost for how to configure your `BaseRule.config` file.

Replace mattermost_webhook_url with the URL of your webhook.

```yaml
mattermost_webhook_url: 'https://xxxxxx/hooks/xxxxxxxxxxxxxxxx'
```

#### How to setup Email?

Sorry Not Support email_format.

Please see https://elastalert2.readthedocs.io/en/latest/ruletypes.html#email for how to configure your `BaseRule.config` file.

[example setting](https://github.com/johnsusek/praeco/issues/245#issuecomment-691523706)

```yaml
smtp_auth_file: '/opt/elastalert/pass/smtp_auth_user.yaml'
```

smtp_auth_user.yaml

```yaml
user: xxx@yahoo.co.jp
password: xxx
```

Google account in advance → Apps that can access your account → Allow less secure apps: Disabled → Enabled When I specified the Gmail address with from_addr and checked the operation, it worked without problems.

```yaml
smtp_auth_file: '/opt/elastalert/smtp/smtp_auth_user.yaml"
```
smtp_auth_user.yaml

```yaml
user: xxx@gmail
password: xxx
```

#### How to setup Jira?

Please see https://elastalert2.readthedocs.io/en/latest/ruletypes.html#jira for how to configure your `BaseRule.config` file.

## Architecture details

![](https://user-images.githubusercontent.com/611996/52892144-90a19300-3155-11e9-8050-cb4a440411a4.png)

Praeco is a vue.js app (hosted in an nginx docker container) that communicates with the [Elastalert API](https://github.com/johnsusek/elastalert-server) (running in another docker container) to view/edit rules.
The elastalert api interacts with the included [Elastalert 2](https://github.com/jertel/elastalert2) python daemon directly for various tasks including testing and silencing rules, and indirectly by modifying or creating
rule files in the rules/ directory.

When you run praeco using the quickstart instructions, it runs these two docker containers, per the docker-compose.yml file.

Praeco uses a fork of the elastalert _api server_, which is why the docker image source is `johnsusek/elastalert`.

NOTE: Only the _api server_ is a fork, the elastalert 2 daemon itself is built from the `master` branch whenever a new version of the `johnsusek/elastalert-server` docker image is created.

Please see the development section below if you're interested in running these services separately.

## Manual/Dev installation

NOTE: If you're just interested in developing Praeco UI features locally (and not changing elastalert 2 or the api), you can skip right to Praeco setup and just run the internal Elastalert server with `docker-compose up elastalert-server` .

---

First, you need a local copy of the elastalert api server running, which itself needs elastalert 2. Start by cloning the neccessary repos

```sh
$ cd
$ git clone https://github.com/jertel/elastalert2.git
$ git clone https://github.com/johnsusek/elastalert-server.git
$ git clone https://github.com/johnsusek/praeco.git
```

### Setting up Elastalert 2

**Python Support version**

- 3.6
- 3.7
- 3.8
- 3.9

**Elasticsearch Support version**

- 7.x

I think it works with 6.x, but I haven't confirmed the operation.

Configure the elastalert 2 `config.yaml` with:
- Your `es_host`
- A unique `writeback_index`
- Change the rules_folder to `rules`

```sh
cd ~/elastalert2
mkdir -p rules rule_templates
chmod -R 777 rules rule_templates
touch rules/BaseRule.config
pip install "setuptools>=11.3"
python setup.py install
cp config.yaml.example config.yaml
vi config.yaml
```

### Setting up the API server

Configure the api server `config.json` with:
- An _absolute path_ to your elastalert 2 folder for `elastalertPath`
- The address of your elasticsearch instance for `es_host`
- The same `writeback_index` from the config.yaml

```sh
# nvm install
# https://github.com/nvm-sh/nvm#install--update-script
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
$ vi ~/.bash_profile

export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm

source ~/.bash_profile

# npm & node install
npm install -g npm@7.2.0
nvm install 14.15.0
```

```sh
cd ~/elastalert-server
vi config/config.json
nvm use "$(cat .nvmrc)"
npm install
npm run start
```

You should see this line if it started successfully:
```
INFO elastalert-server: Server:  Server started
```

### Setting up praeco

Finally, run praeco:

```sh
# No need to implement if the environment is the same as elastalert-server
# nvm install
# https://github.com/nvm-sh/nvm#install--update-script
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
$ vi ~/.bash_profile

export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm

source ~/.bash_profile

# npm & node install
npm install -g npm@7.2.0
nvm install 14.15.0
```

```sh
cd ~/praeco
nvm use "$(cat .nvmrc)"
npm install
export PRAECO_ELASTICSEARCH=<your elasticsearch ip>
npm run serve
```

You should now see the UI running at [http://localhost:8080](http://localhost:8080).

If you have any difficulties please open a github issue with your problem.



## Maintainers

<table>
  <tr>
    <td align="center"><a href="https://github.com/johnsusek"><img src="https://avatars3.githubusercontent.com/u/611996?v=4" width="100px;" alt=""/><br /><sub><b>John Susek</b></sub></a></td>
    <td align="center"><a href="https://github.com/nsano-rururu"><img src="https://avatars1.githubusercontent.com/u/22293449?v=4" width="100px;" alt=""/><br /><sub><b>Naoyuki Sano</b></sub></a></td>
  </tr>
</table>

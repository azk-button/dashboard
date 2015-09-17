Reportr
=========

> "Your life's personal dashboard."

[![Deploy](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy)

Reportr is a complete application which works like a dashboard for tracking events in your life (using a very simple API). With a simple interface, it helps you track and display your online activity or your real-life activity (with hardware trackers or applications like Runkeeper), some trackers are available on [this organization](https://github.com/Reportr).

The project is entirely open source and you can host your own Reportr instance on your own server or Heroku. 

[![Screen Preview](./preview.png)](./preview.png)

## Running locally

Click the button bellow to quickly and safely install this project on your local machine.

[![Run project](https://s3-sa-east-1.amazonaws.com/assets.azk.io/run-project.png)](http://run.azk.io/start/?repo=azk-button/reportr&ref=azkfile)

The `Run Project` button employs `azk`, a lightweight open source orchestration tool that will automatically isolate and configure the application's environment for you.

Learn more about `azk` [here](http://azk.io).

## Deploying to DigitalOcean

After you run this project locally using [`Run Project` button](#running-locally), deploying to [DigitalOcean](http://digitalocean.com/) is very simple.

First, be sure you have SSH keys configured in your machine. If you don't have it yet (or if you aren't sure about it), just follow steps 1 and 2 of [this tutorial](https://help.github.com/articles/generating-ssh-keys/).

Next, put your [personal access token](https://cloud.digitalocean.com/settings/applications) into a `.env` file:

```bash
$ cd path/to/the/project
$ echo "DEPLOY_API_TOKEN=<YOUR-PERSONAL-ACCESS-TOKEN>" >> .env
```

Then, just run the following:

```bash
$ azk shell deploy
```

The `Run Project` button employs `azk`, a lightweight open source orchestration tool that will automatically isolate and configure the application's environment for you.

Find instructions for further resources (mostly customizations) to deploy to DigitalOcean using `azk` [here](http://docs.azk.io/en/deploy/README.html).

## API and Events

Reportr uses an HTTP REST API to track events. Datas are always JSON encoded.

| Endpoint | HTTP Method | Description | Arguments |
| -------- | ----------- | ----------- | --------- |
| /api/infos | GET | Get informations about this instance |  |
| /api/types | GET | Return all event types |  |
| /api/events | POST | Post a new event | `<string>type`, `<object>properties` |
| /api/events | GET | List all events | `<string>type`, `<int>start(0)`, `<int>limit` |
| /api/stats/categories | GET | Get categorized events stats | `<string>type`,`<string>field` |
| /api/stats/time | GET | Get time stats | `<string>type`,`<string>fields`, `<string>interval`, `<string>func` |
| /api/reports | POST | Create a new report | `<string>title` |
| /api/reports | GET | List all reports |  |
| /api/report/:id | PUT | Update a report | `<string>title`, `<array>visualizations` |
| /api/report/:id | DELETE | Remove a report |  |
| /api/alerts | GET | List all alerts |  |
| /api/alerts | POST | Create an alert | `<string>type`, `<string>eventName`, `<string>condition`, `<string>title` |

#### Special Events

| Name | Description | Properties |
| ---- | ----------- | ---------- |
| reportr.alert | Triggered when an alert is triggered | `<string>type`, `<string>eventName` |


## Configuration

Reportr is configured using environment variables.

| Name | Description |
| ---- | ----------- |
| PORT | Port for running the application, default is 5000 |
| MONGODB_URL | Url for the mongoDB database |
| REDIS_URL | (Optional) Url for a redis database when using worker mode |
| AUTH_USERNAME | Username for authentication |
| AUTH_PASSWORD | Password for authentication |

See [types](#types) for informations about alert configurations.

## Events

An event represent something to monitor at a defined date. For example if I'm monitoring the temperature in my home, I'll post an event `home.temperature` with a property `temp`:

```
$ curl -X POST -H "Content-Type: application/json" --data '{ "type":"home.temperature", "properties": { "temperature": 66 } }' http://localhost:5000/api/events
```

## Visualizations

A visualization is a configured way to show data, for example in a pie, bar chart or time graph.

#### Types

| Type | Description |
| ---- | ----------- |

#### Templates

Visualizations accept templates as most of rendering options. Template are processed using [lodash's _.template method](http://lodash.com/docs#template) with some special functions:

- `$.date(date)`: returns a beautiful date

## Alerts

Reportr lets you configure alerts to be triggered when specific condition is valid at a specific interval.

#### Types

| Type | Description | Configuration |
| ---- | ----------- | ------------- |
| webhook | Post an HTTP request to a specific url with the data encoded in the body | |
| mail | Send an email notification | `<string>MAIL_SERVICE`, `<string>MAIL_USERNAME`, `<string>MAIL_PASSWORD`, `<string>MAIL_FROM` |
| sms | Send a text message notification | `<string>TWILIO_SID`, `<string>TWILIO_TOKEN`, `<string>TWILIO_FROM` |

#### Condition

Condition for alerts are really easy to write, for example: `COUNT > 9`, this condition will be valid if at least 10 events have been posted in the alert interval. Conditions can also use the event object, for example: `event.temperature > 80`.

## Trackers

| Description | Link |
| ---- | ----------- |
| Google Chrome Navigation | https://github.com/Reportr/tracker-googlechrome |
| Home ambient (temperature, humidity, light) | https://github.com/Reportr/tracker-home-ambient |
| Memory and CPU of computer | https://github.com/Reportr/tracker-machine |
| Battery data | https://github.com/hughrawlinson/tracker-machine-battery |

## Scale it

Reportr can easily be scaled on Heroku (and compatibles), use the `REDIS_URL` to enable a task queue between **workers** and **web** processes.



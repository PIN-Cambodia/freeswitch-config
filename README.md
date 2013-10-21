# freeswitch-config (Development)

Use this branch on a development machine with FreeSwitch installed locally to test the app.

## Version Control

Make sure you're on the development branch

```
sudo -u freeswitch git checkout development
```

### Simulating Calls

Sign in to your dev-box FreeSwitch as 855977100860@local_ip using a SIP client such as QuteCom or make a new profile under `directory/default. The sign-in name is important as it will simulate the caller_id for the calling party.

#### Incoming

Placing a call to 2442 will simulate an incoming call to Chibi. You'll need to have Vibi and Chibi running locally as well to get this to work.

#### Outgoing

Placing a call to 2444 will simulate an outgoing call to whoever is in $${test_number} on your FreeSwitch development box though the external SIP provider. See passwords.xml for configuration.

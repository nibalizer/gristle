Gristle
=======

Gristle is a small application that polls GitHub repository events and relays
them to SSH-connected clients similar in fashion to the gerrit-event-stream.
There is a push currently in the OpenStack CI community to add GitHub support
to Zuul using webhooks.  My primary motivation for implementing this was to
create a stream service similar to what Gerrit offers, so that downstreams
that are unable to use webhooks due to security constraints (e.g. corporate
firewalls) have an alternative.

Installation
============

    apt-get install -y python-virtualenv
    virtualenv venv
    source venv/bin/activate
    pip install -r requirements.txt
    sudo python setup.py install

Operation
=========

    gristle --config=path/to/config.yaml

Configuration
=============

There are plans to create '/etc/gristle/' directory hierarchy upon
installation, but for now you'll want to create your directory, e.g.

    etc/
    └── gristle
        ├── config.yaml
        └── ssh
            ├── authorized_keys
            └── hostkey


config.yaml
-----------

This will contain all the configuration information Gristle needs to run the
service.

    ---
    sshd:
      host_key: etc/gristle/ssh/hostkey
      authorized_keys: etc/gristle/ssh/authorized_keys
      port: 2222
    accounts:
      - url: https://api.github.com
        username: $USERNAME
        password: $PASSWORD
        repos:
          - name: foo/foo
          - name: bar/foo
            polling: 60
          - name: bar/bar

ssh/authorized_keys
--------------------

This will contain all the public keys Gristle is willing to accept.  The
format is:

    key-type key-contents login-username

ssh/hostkey
-----------

This is the hostkey the Gristle SSH server will use.  You'll want to generate
this key yourself using the ssh-keygen program.

Todo
====

 * Flesh out installation process and upload to pypi
 * Write some unit tests... :/
 * Add token-based authentication
 * Add better documentation describing configuration



Event example
-------------


This is an example event you can pick up on the ssh pipeline. Future work will translate this to gerritish format so it can be used by zuul.


```json
{
  "payload": {
    "action": "created",
    "comment": {
      "body": "poke",
      "url": "https://api.github.com/repos/nibalizer/finglonger/issues/comments/253876644",
      "created_at": "2016-10-14T18:03:04Z",
      "html_url": "https://github.com/nibalizer/finglonger/pull/15#issuecomment-253876644",
      "updated_at": "2016-10-14T18:03:04Z",
      "user": {
        "following_url": "https://api.github.com/users/nibalizer/following{/other_user}",
        "events_url": "https://api.github.com/users/nibalizer/events{/privacy}",
        "organizations_url": "https://api.github.com/users/nibalizer/orgs",
        "url": "https://api.github.com/users/nibalizer",
        "gists_url": "https://api.github.com/users/nibalizer/gists{/gist_id}",
        "html_url": "https://github.com/nibalizer",
        "subscriptions_url": "https://api.github.com/users/nibalizer/subscriptions",
        "avatar_url": "https://avatars.githubusercontent.com/u/195179?v=3",
        "repos_url": "https://api.github.com/users/nibalizer/repos",
        "received_events_url": "https://api.github.com/users/nibalizer/received_events",
        "gravatar_id": "",
        "starred_url": "https://api.github.com/users/nibalizer/starred{/owner}{/repo}",
        "site_admin": false,
        "login": "nibalizer",
        "type": "User",
        "id": 195179,
        "followers_url": "https://api.github.com/users/nibalizer/followers"
      },
      "id": 253876644,
      "issue_url": "https://api.github.com/repos/nibalizer/finglonger/issues/15"
    },
    "issue": {
      "labels": [],
      "number": 15,
      "assignee": null,
      "repository_url": "https://api.github.com/repos/nibalizer/finglonger",
      "closed_at": null,
      "id": 177532394,
      "title": "\"DNM: testing travis\"",
      "pull_request": {
        "url": "https://api.github.com/repos/nibalizer/finglonger/pulls/15",
        "diff_url": "https://github.com/nibalizer/finglonger/pull/15.diff",
        "html_url": "https://github.com/nibalizer/finglonger/pull/15",
        "patch_url": "https://github.com/nibalizer/finglonger/pull/15.patch"
      },
      "comments": 11,
      "state": "open",
      "body": null,
      "labels_url": "https://api.github.com/repos/nibalizer/finglonger/issues/15/labels{/name}",
      "events_url": "https://api.github.com/repos/nibalizer/finglonger/issues/15/events",
      "comments_url": "https://api.github.com/repos/nibalizer/finglonger/issues/15/comments",
      "html_url": "https://github.com/nibalizer/finglonger/pull/15",
      "updated_at": "2016-10-14T18:03:04Z",
      "user": {
        "following_url": "https://api.github.com/users/nibalizer/following{/other_user}",
        "events_url": "https://api.github.com/users/nibalizer/events{/privacy}",
        "organizations_url": "https://api.github.com/users/nibalizer/orgs",
        "url": "https://api.github.com/users/nibalizer",
        "gists_url": "https://api.github.com/users/nibalizer/gists{/gist_id}",
        "html_url": "https://github.com/nibalizer",
        "subscriptions_url": "https://api.github.com/users/nibalizer/subscriptions",
        "avatar_url": "https://avatars.githubusercontent.com/u/195179?v=3",
        "repos_url": "https://api.github.com/users/nibalizer/repos",
        "received_events_url": "https://api.github.com/users/nibalizer/received_events",
        "gravatar_id": "",
        "starred_url": "https://api.github.com/users/nibalizer/starred{/owner}{/repo}",
        "site_admin": false,
        "login": "nibalizer",
        "type": "User",
        "id": 195179,
        "followers_url": "https://api.github.com/users/nibalizer/followers"
      },
      "milestone": null,
      "locked": false,
      "url": "https://api.github.com/repos/nibalizer/finglonger/issues/15",
      "created_at": "2016-09-16T22:22:50Z",
      "assignees": []
    }
  },
  "created_at": "2016-10-14T18:03:05Z",
  "actor": {
    "url": "https://api.github.com/users/nibalizer",
    "display_login": "nibalizer",
    "avatar_url": "https://avatars.githubusercontent.com/u/195179?",
    "gravatar_id": "",
    "login": "nibalizer",
    "id": 195179
  },
  "id": "4712828768",
  "repo": {
    "url": "https://api.github.com/repos/nibalizer/finglonger",
    "id": 65847962,
    "name": "nibalizer/finglonger"
  },
  "type": "IssueCommentEvent",
  "public": true
}
```



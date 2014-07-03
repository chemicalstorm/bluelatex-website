---
layout: default
Title: Configuration
---

Configuration File Layout
-------------------------

The \BlueLaTeX server is organized in a bunch of [OSGi](http://www.osgi.org/Main/HomePage) bundles making it really modular.
The configuration of each bundle is made in its own directory, with respect to this modular architecture.

The directory layout for configuration files is organized under one directory for each bundle.
The directory in which the configuration of a bundle lies is simply the bundle identifier.
Standard bundles are:

 - `org.gnieh.blue.common` the common utility bundle (http server, database configuration, ...),
 - `org.gnieh.blue.core` the core \BlueLaTeX features (user management, paper management, ...),
 - `org.gnieh.blue.sync` the standard \BlueLaTeX synchronization server,
 - `org.gnieh.blue.compile` the \LaTeX compilation bundle,
 - `org.gnieh.blue.web` the standard web client.

Below is detailed the configuration of each standard bundle.

Common Bundle `org.gnieh.blue.common`
-------------------------------------

```scala
http {

  # the port the http server listens to
  port = 8080

  # the host to bind the http server to
  host = localhost

}

recaptcha {
  # no ReCaptcha key by default
  # the private key
  # private-key = "my super private key"
  # the public key (for clients)
  # public-key = "my awesome public key"
}

# \BlueLaTeX service configuration. This section contains the generic
# configuration keys of the service. Each bundle can use these configuration keys
blue {

  # the base URL hosting the \BlueLaTeX service.
  # this is used in emails sent to users or to generate links to the service.
  base-url = "http://${http.host}:${http.port}/"

  # the base directory where the working data are saved
  data = "/var/lib/blue"

  # the session timeout
  session-timeout = 15 minutes

  # Paper specific keys
  paper {

    # the directory where the paper data are saved
    directory = ${blue.data}/papers

    # the directory containing the cls files
    classes = ${blue.data}/classes

  }

  # Template specific keys
  template {

    # the base directory where the templates are located
    directory = ${blue.data}/templates

  }

  # Rest API specific configuration keys
  api {

    # the prefix of this \BlueLaTeX installation
    # if it is installed at the root of the domain, then leave this string empty
    path-prefix = ""

  }

  # All configuration keys related to the permission model
  # There are 5 predefined roles in \BlueLaTeX:
  #  - Author
  #  - Reviewer
  #  - Guest
  #  - Other
  #  - Anonymous
  # However permissions assigned to each role can be tuned for each paper.
  # The list of valid permissions is:
  #  - `edit` allows for collaboratively editing the paper,
  #  - `compile` allows for compiling the paper,
  #  - `configure` allows for configuring the paper settings like compiler settings for example (if any),
  #  - `publish` allows for using the publication feature (like creating a tag or sending to arXive),
  #  - `download` allows for downloading the source files of the paper,
  #  - `read` allows for reading the paper source but not for editing it,
  #  - `view` allows for viewing the rendered paper but not the source,
  #  - `comment` allows for commenting on a paper (either the rendered view or the source),
  #  - `chat` allows for interacting with other connected users in the chat,
  #  - `fork` allows for creating a derivative of this paper,
  #  - `change-phase` allows for changing the current phase of the paper.
  permissions {

    # Defines the basic permissions to assign to a private paper, this can be changed later
    # in any existing paper. If no permission entity exists, these permission will be
    # taken whenever a permission check is performed.
    private-defaults {
      # authors can do anything
      author = [ edit, compile, configure, publish, download, read, view, comment, chat, fork, change-phase ]
      # reviewers may read and comment
      reviewer = [ compile, view, comment ]
      # guests may only read
      guest = [ compile, view ]
      # other people cannot do anything
      other = []
      # anonymous people cannot do anything either
      anonymous = []
    }

    # Defines the basic permissions to assign to a public paper, this can be changed later
    # in any existing paper. If no permission entity exists, these permission will be
    # taken whenever a permission check is performed.
    public-defaults {
      # authors, reviewers, guest, other and anonymous can do anything
      author = [ edit, compile, configure, publish, download, read, view, comment, chat, fork, change-phase ]
      reviewer = ${blue.permissions.public-defaults.author}
      guests = ${blue.permissions.public-defaults.author}
      other = ${blue.permissions.public-defaults.author}
      anonymous = ${blue.permissions.public-defaults.author}
    }

  }

}

# The email configuration. For possible configuration keys,
# see https://javamail.java.net/nonav/docs/api/
mail {

  smtp {

    # the smtp server host name
    host = localhost

    # the smtp server port
    port = 25

    # additional configuration keys specific to \BlueLaTeX
    # when a user and a password are required by the smtp server
    # user = username
    # password = my_password

  }

  # the address from which users receive emails
  from = "no-reply@bluelatex.gnieh.org"

}

# CouchDB connectivity configuration keys
couch {

  # the host name where the couchdb instance runs
  hostname = "localhost"

  # the couchdb server port
  port = 5984

  # should we connect to couchdb via ssl
  ssl = false

  # the timeout when waiting for the server response
  timeout = 20 seconds

  # the couchdb database administrator name
  admin-name = "admin"

  # the couchdb database administrator password
  admin-password = "admin"

  # define the standard database names used by the core \BlueLaTeX service.
  # other bundles can use this configuration as well
  database {

    # the name of the database in which the \BlueLaTeX specific user data are saved
    blue_users = "blue_users"

    # the name of the database in which the papers data are saved
    blue_papers = "blue_papers"

  }

  # the design specific keys
  design {

    # the directory containing the design documents
    dir = ${blue.data}/designs

  }

  # user specific configuration
  user {

    # the default roles assigned to a user when created */
    roles = [ "blue_user" ]

    # the password reset token validity
    token-validity = 1 day

  }

}
```

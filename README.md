# role-asdf

### Master:
[![role-asdf](https://github.com/osx-provisioner/role-asdf/actions/workflows/push.yml/badge.svg?branch=master)](https://github.com/osx-provisioner/role-asdf/actions/workflows/push.yml)

### Production:
[![role-asdf](https://github.com/osx-provisioner/role-asdf/actions/workflows/push.yml/badge.svg?branch=production)](https://github.com/osx-provisioner/role-asdf/actions/workflows/push.yml)

Ansible role that installs the asdf version manager on OSX machines.

### Notes:
- See the [asdf documentation](https://asdf-vm.com/) for further details about this tool.
- This role was inspired and derived from [this](https://github.com/cimon-io/ansible-role-asdf) fantastic Ansible role for Linux machines.

Requirements
------------

- Homebrew must aleady be present on the machine ([geerlingguy.mac.hombrew](https://github.com/geerlingguy/ansible-collection-mac) is a great solution for this).
- See the [homebrew website](https://brew.sh/) for further details about this tool.

Role Variables
--------------

Each plugin should be configured in the following format:

```yaml
asdf_plugins:
  - name: "erlang"        # a plugin name
    environment: {}       # an optional dictionary of environment variables for build configuration
    repository: ""        # a plugin repository, optional
    versions:             # a list of versions to install
      - 23.3.3
      - 23.3.4
    delete_versions: []   # a list of existing versions that will be removed
    global: 23.3.4        # set as a global version, optional
```

The following variables are also configured:
- `asdf_version`:
  - Sets the git tag of asdf.
- `asdf_user`:
    - Sets a user for which the role is installed.  This user needs to be able to run Homebrew to install dependencies required for asdf plugins.
    - For further details see the [geeringguy.mac.homebrew](https://github.com/geerlingguy/ansible-collection-mac) repository. (You may need to set the `homebrew_user` variable as well.)
    - The default value will work fine if you're simply installing for the current user.
- `asdf_user_home`:
    - Sets the user's home folder location, if needed.  Take care to set it explicitly when installing for another user.
- `asdf_homebrew_retries`:
    - Sets the number of attempts homebrew will make to install dependencies. (For flaky network connections.)
- `asdf_legacy_version_file`:
    - Specifies if plugins which support this feature should read the version files used by other version managers (e.g. `.ruby-version` in the case of Ruby's rbenv).
- `asdf_darwin_global_dependencies`:
    - Sets homebrew packages which will be installed with any asdf install.
- `asdf_darwin_optional_dependencies`:
    - Sets additional requirements that will be installed with any asdf plugin install.

[See The Default Values](defaults/main.yml)

### Plugin Specific Configuration

Plugins can also have their own specific dependencies and extra configuration.
To add extra dependencies, simply append your plugin to this list, and add the homebrew dependencies:

```yaml
asdf_darwin_plugin_dependencies:
  erlang: ["autoconf", "fop", "libyaml", "libxslt", "libtool", "unixodbc", "wxmac"]
  nodejs: ["gpg", "gnupg"]
  php: []
  ruby: []
  python: ["sqlite3", "xz", "zlib"]
```

To create additional custom configurations simply create a task file in the [plugin_tasks](./tasks/plugins_darwin/plugin_tasks) folder in the format: `(PLUGIN_NAME).yaml`
(i.e. `python.yml`, `nodejs.yml`)

Dependencies
------------

None

Example Playbook
----------------

```yaml
- hosts: all
  roles:
  - role: elliotweiser.osx-command-line-tools
  - role: geerlingguy.mac.homebrew
  - role: osx_provisioner.asdf
    asdf_plugins:
    - name: "erlang"
      versions: ["23.3.4", "23.3.3"]
      global: "23.3.4"
      environment:
        KERL_CONFIGURE_OPTIONS: "--without-javac --with-ssl=/usr/local/opt/openssl@1.1 --disable-hipe"
        CFLAGS: "-O2 -g -fno-stack-check"
    - name: "python"
      versions: "3.9.1"
```

License
-------

MIT

(asdf is also licensed as MIT)

Author Information
------------------

Niall Byrne <niall@niallbyrne.ca>

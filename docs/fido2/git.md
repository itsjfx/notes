# FIDO2 + Git

* A script to create an SSH key and on-board it to GitHub
* Supports authentication and signing
* Thanks Mike
* Usage: `./bin/extras/git/git-fido-ssh itsjfx-github@x x@x.com itsjfx`

https://github.com/bash-my-aws/bash-my-aws/tree/dev/bin/extras/git

Some fixes:

```
diff --git a/bin/extras/git/git-fido-ssh b/bin/extras/git/git-fido-ssh
index 3e4fbe1..52b1c00 100755
--- a/bin/extras/git/git-fido-ssh
+++ b/bin/extras/git/git-fido-ssh
@@ -5,7 +5,7 @@
 #                updates Git user details based on realm, and manages SSH configurations.
 #
 # Usage: ./git-fido-ssh keyid@realm [git-email] [git-name]
-# 
+#
 #       keyid: Any word to identify your Yubikey to you (e.g. key1)
 #       realm: security domain or organization (e.g. personal, work, mycompany)
 #
@@ -13,13 +13,16 @@
 #
 # - GitHub CLI (gh) - https://cli.github.com/
 
+# set -eu -o pipefail
+set -x
+
 git-fido-ssh() {
     local keyid_realm="$1"
     local git_email="$2"
     local git_name="$3"
 
-    local keyid="${keyid_realm%@*}"
-    local realm="${keyid_realm#*@}"
+    local realm="${keyid_realm%@*}"
+    local keyid="${keyid_realm#*@}"
     local key_path="$HOME/.ssh/${keyid_realm}"
     local git_config_path="$HOME/.gitconfig_${realm}"
     local ssh_config_file="$HOME/.ssh/config.d/${realm}"
@@ -41,7 +44,7 @@ git-fido-ssh() {
             fi
         else
             # Fetch the current GitHub username
-            echo "$auth_status" | grep 'Logged in to github.com as'
+            echo "$auth_status" | grep 'Logged in to github.com'
 
             read -p "Do you want to continue with this account? (y/n) " choice
 
@@ -88,10 +91,10 @@ git-fido-ssh() {
     fi
 
     if [[ ! -f "$key_path" ]]; then
-        ssh-keygen -t ecdsa-sk -b 521 -C "${keyid_realm}" -f "$key_path"
+        ssh-keygen -t ed25519-sk -C "${keyid_realm}" -f "$key_path"
         symlink_path="$(dirname $key_path)/${keyid_realm}"
         echo "SSH key generated at $key_path"
-        (cd $(dirname $key_path) && ln -sf "$keyid_realm" "${realm}") # so we can use generic name
+        # (cd $(dirname $key_path) && ln -sf "$keyid_realm" "${realm}") # so we can use generic name
     else
         echo "Error: Key file $key_path already exists."
         return 1
@@ -100,7 +103,8 @@ git-fido-ssh() {
     eval "$(ssh-agent -s)"
     ssh-add "$key_path"
     check_and_switch_github_account
-    gh ssh-key add "${key_path}.pub" -t "${keyid_realm}"
+    gh ssh-key add "${key_path}.pub" -t "${keyid_realm}" --type authentication
+    gh ssh-key add "${key_path}.pub" -t "${keyid_realm}" --type signing
     create_ssh_config
 
     if [[ -f "$git_config_path" ]]; then
```
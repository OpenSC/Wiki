# The OpenSC wiki

This is an OpenSC wiki. Authoritative source for the wiki rendered on Github
OpenSC repository:

https://github.com/OpenSC/OpenSC/wiki

## Why?

It used to be accessible for everyone to edit, until we got some malicious
actors trying to sneak in malware download links.

Now, it lives in separate repository https://github.com/OpenSC/Wiki where
we accept contributions through pull requests.

## How to update wiki

### Contributors

Open a pull request with your changes as you would do with your code changes
in this repository.

Do not use the "Edit" button on the wiki pages directly (accessible only for
project members now).

### Maintainers

This git repository is an authoritative source of truth. Do NOT use the Edit
button on the wiki.

Once the commit is merged to this repository, it needs to be manually pushed
to the OpenSC wiki. Recommended approach is to configure the opensc wiki
remote in your git client:
```
git remote add opensc.wiki git@github.com:OpenSC/OpenSC.wiki.git
```
and once there is a change, pull it locally and push it to the opensc.wiki
```
git pull origin master
git push opensc.wiki
```

If the branches diverge for some reason, please investigate where did the
changes come from and sync the repositories.

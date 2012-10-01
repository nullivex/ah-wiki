# Exceptions

**This module only works for node.js >= v0.8.0, as it makes use of domains**

actionHero will catch any uncaught exceptions which take place within actions or tasks.  Theses exceptions will be logged along with any relevant details which can be captured about the connection making the request.

When this happens:

- web clients will be sent the 500 (server error) header as defined in `api.configData.general.serverErrorMessage`
- Exceptions created in tasks will also be logged, and the task will return
- If the Exception occured within a periodic task, the task will be re-enqueud.
- keep in mind that any applicaton-wide settings which may have been modified in this erronious action/task will not be rolled-back

Other exceptions, perhaps occurring in an initializer, will not be caught.  These are probably serious and should be investigated.
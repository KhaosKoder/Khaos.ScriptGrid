The ScriptGrid is a novel implementation of a very simple tool.

The idea is simply as follows:

The "grid" means that a bunch of these applications work together. They communiate using signalR which allows them to talk to each other or to broadcast to the entire group. 

I guess we need some  sort of registry. Maybe model this on kafka, where the first node becomes a leader and everyone reports to it. If it fails, the second node automatically becomes the leader (until a reboot)

The app is responsible for doing just one thing:
Running scripts (SQL or Powershell)
The app may use Flows or Pipelines. 

It contains a full blown scheduler (cron-like) to schedule tasks. 

Adding new tasks may mean building c# code for that. OR it might mean that you simply add scripts and define the registration in appsettings.


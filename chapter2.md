# Chapter 2

Bolt deals with replication in a completely different way compared to most (all?) other networking solutions for Unity. Instead of manually writing code for serializing data, for example using **OnSerializeNetworkView** in the built in Unity networking solution, Bolt lets you define transforms, animations and and custom properties for it to automatically replicate over the network - you don't need to write any code what so ever. In fact in this entire tutorial we do not write any type of low level networking code, Bolt handles all of it for you.


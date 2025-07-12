+++
title = "One handler and two storages"
date = "2025-01-01"

[taxonomies]
tags = ["ugc", "middle_python"]

+++

In the UGC module, we may face a distributed transaction problem: how can we write to two storages from a single handler?

The answer is—we can’t, at least not directly. Instead, we must choose one storage as the master and then propagate data from this master storage to all related storages in a controlled way.


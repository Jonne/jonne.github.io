---
layout: post
title:  "Sitecore Commerce 8.2 cache invalidation explained"
date: 2019-02-25 13:21
author: jonnekats
comments: true
keywords: Sitecore 82, commerce, cache, invalidation, explained
categories: [Sitecore]
---
For one of our Sitecore Commerce customers it is really important that product updates are processed and reflected on their website as soon as possible. In order to help them we really needed to understand where the product data is cached and how this is invalidated. In this post I will share our findings, which apply to Sitecore Commerce 8.2 & 8.2.1.

The catalog data is cached on the following levels:

![Caching layers](/assets/images/commerce-caching/cachinglayers.gif)

### Commerce Server
The catalog data is cached in the `CatalogCache`, which basically caches all the datasets that are retrieved from the database.  

**When cleared?**

The commerce server catalog cache uses an expiration that is defined in the `App_Config\CommerceServer\CommerceServer.catalog.config` file. The `catalog -> cache` element contains several cache timeout attributes. See [Cache Configuration Properties ](https://docs.microsoft.com/en-us/previous-versions/commerce-server/aa144659(v=cs.90)) for more information.

Besides the expiration the entire catalog cache is cleared when a `CommerceCacheRefreshEvent` is queued in the EventQueue.  

### Sitecore Commerce Connect
Commerce connect has an ExternalIdCache and a TemplateHierarchyCache. The ExternalIdCache caches the mapping between the SitecoreId of a catalog item and the productid/categoryid. The TemplateHierarchyCache caches the sitecore templates which are generated based on the category and product definitions. 

**When cleared?**

The ExternalIdCache and a TemplateHierarchyCache in Sitecore commerce connect have no expiration. 

The entire ExternalIdCache and TemplateHierarchyCache are cleared when a `CommerceCacheRefreshEvent` is queued in the EventQueue.  

### Sitecore
The Sitecore data API caches the product data in the same it does normal items, which is in the following caches:

- ItemCache
- DataCache
- StandardValuesCache
- PathCache
- ItemPathsCache

**When cleared?**

The Sitecore data API has no expiration and caches items as long as they are valid (Based on published period). Specific items are removed from the cache when an `ItemSavedEvent` is queued in the EventQueue (This happens when the related items are published). This cache is also cleared when a `CommerceCacheRefreshEvent` is queued in the EventQueue.  

**What is this CommerceCacheRefreshEvent you've mentioned?**
The `CommerceCacheRefreshEvent` mentioned above is used to make sure that the related caches on all servers are cleared when a change is made on one of the servers. This event is essential for the commerce cached to stay consistent. This event is used to clear different types of caches. The event has a CacheType that can contain the following values:

- "sitecore" - If the event also contains an ItemID, the invidual item is removed from the sitecore caches. If not, the entire sitecore cache is cleared. 
- "catalogcache" - The entire catalog cache is cleared and if the event also contains an ItemID, the invidual item is removed from the sitecore caches. If not, the entire sitecore cache is cleared. 
- "allcaches" - All sitecore and commerce caches are cleared
- "commercecaches" - All sitecore and commerce caches are cleared, except for the catalog cache

**When is this CommerceCacheRefreshEvent queued?**
This event is queued when changes are made using the merchandising manager. 

The event is also queued during an incremental index update. When items in the catalog are updated the `CatalogIntervalAsynchronousStrategy` will retrieve a list of modified catalog items from the ExternalId table. It will then queue a `CommerceCacheRefreshEvent` with CacheType catalogcache. This will make sure that the entire catalog cache is cleared on every server. After this, it will queue `CommerceCacheRefreshEvent` with CacheType sitecore and an ItemID for every catalog item, which makes sure that the catalog item is removed from the sitecore cache from every server. 
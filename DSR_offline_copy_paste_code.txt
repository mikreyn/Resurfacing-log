/* Offline support is scoped to dsr.html only; it does not control the activity-log app. */
const CACHE='track-machine-dsr-shell-v1';
const PAGE=new URL('dsr.html',self.location.href).href;
self.addEventListener('install',event=>event.waitUntil((async()=>{const cache=await caches.open(CACHE);await cache.add(new Request(PAGE,{cache:'reload'}));await self.skipWaiting();})()));
self.addEventListener('activate',event=>event.waitUntil((async()=>{for(const name of await caches.keys())if(name.startsWith('track-machine-dsr-shell-')&&name!==CACHE)await caches.delete(name);await self.clients.claim();})()));
self.addEventListener('fetch',event=>{if(event.request.method!=='GET')return;const url=new URL(event.request.url),page=new URL(PAGE);if(url.origin!==page.origin||url.pathname!==page.pathname)return;event.respondWith((async()=>{const cache=await caches.open(CACHE);try{const response=await fetch(event.request);if(response.ok)await cache.put(PAGE,response.clone());return response;}catch(error){const saved=await cache.match(PAGE);if(saved)return saved;throw error;}})());});

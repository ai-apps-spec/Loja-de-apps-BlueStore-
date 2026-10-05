const CACHE_NAME = 'bluestore-app-shell-v1';
const APP_SHELL_URL = new URL('./', self.registration.scope).href;

self.addEventListener('install', event => {
  evento.waitUntil((async () => {
    const cache = await caches.open(CACHE_NAME);
    tentar {
      await cache.add(new Request(APP_SHELL_URL, { cache: 'reload' }));
    } catch (erro) {
      A instalação pode prosseguir mesmo quando a rede estiver temporariamente indisponível.
    }
    aguarde self.skipWaiting();
  })());
});

self.addEventListener('activate', event => {
  evento.waitUntil((async () => {
    const keys = await caches.keys();
    await Promise.all(keys.filter(key => key.startsWith('bluestore-app-shell-') && key !== CACHE_NAME).map(key => caches.delete(key)));
    aguarde self.clients.claim();
  })());
});

self.addEventListener('fetch', event => {
  const request = event.request;
  Se (request.method !== 'GET' || request.mode !== 'navigate') retorne;
  const requestUrl = new URL(request.url);
  Se (requestUrl.origin !== self.location.origin) retorne;

  evento.respondWith((async () => {
    tentar {
      const resposta = await fetch(requisição);
      se (resposta && resposta.ok) {
        const cache = await caches.open(CACHE_NAME);
        await cache.put(APP_SHELL_URL, response.clone());
      }
      retornar resposta;
    } catch (erro) {
      return (await caches.match(APP_SHELL_URL)) || new Response('BlueStore indisponível offline.', {
        status: 503,
        cabeçalhos: { 'Content-Type': 'text/plain; charset=utf-8' }
      });
    }
  })());
});

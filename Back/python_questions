# Кеширование данных в Python-приложениях

## Паттерны

### 1. **Cache-Aside (Вытаскивание из кеша)**
   - **Описание:** При использовании этого паттерна, приложение явно управляет кешем. При запросе данных сначала происходит поиск в кеше, и если данные отсутствуют, они извлекаются из источника данных и затем сохраняются в кеше.
   - **Примеры библиотек:** `cachetools`, `functools.lru_cache`.

### 2. **Write-Through (Прямая запись)**
   - **Описание:** Данные обновляются в кеше сразу же после обновления в основном хранилище. Это гарантирует согласованность данных между кешем и хранилищем.
   - **Примеры библиотек:** `Django Cache Framework`, `SQLAlchemy` с использованием `dogpile.cache`.

### 3. **Write-Behind Caching (Отложенная запись)**
   - **Описание:** Приложение сначала обновляет данные в кеше, а затем асинхронно обновляет основное хранилище. Это уменьшает нагрузку на основное хранилище, ускоряя операции записи.
   - **Примеры библиотек:** Некоторые NoSQL базы данных, такие как Redis, могут поддерживать отложенную запись.

### 4. **Read-Through (Прямое чтение)**
   - **Описание:** При отсутствии данных в кеше, они автоматически извлекаются из основного хранилища и сохраняются в кеше перед возвращением результатов.
   - **Примеры библиотек:** `Django Cache Framework`, `SQLAlchemy` с использованием `dogpile.cache`.

### 5. **Refresh-Ahead (Проактивное обновление)**
   - **Описание:** Предварительное обновление данных в кеше перед истечением их срока действия. Это снижает время ожидания клиента, так как данные уже доступны в кеше.
   - **Примеры библиотек:** `cachetools`.

## Инструменты

### 1. **Memcached**
   - **Описание:** Высокопроизводительная система кеширования в памяти. Широко используется для хранения пар ключ-значение в распределенной среде.
   - **Пример использования:**
     ```python
     import memcache
     mc = memcache.Client(['127.0.0.1:11211'], debug=0)
     mc.set("some_key", "Some value")
     value = mc.get("some_key")
     ```

### 2. **Redis**
   - **Описание:** Ин-memory база данных и система кеширования. Поддерживает различные структуры данных, включая строки, хэши, списки и другие.
   - **Пример использования:**
     ```python
     import redis
     r = redis.StrictRedis(host='localhost', port=6379, db=0)
     r.set('some_key', 'Some value')
     value = r.get('some_key')
     ```

### 3. **Cachetools**
   - **Описание:** Библиотека для кеширования в Python с поддержкой различных стратегий, таких как LRU (Least Recently Used), LFU (Least Frequently Used) и других.
   - **Пример использования:**
     ```python
     from cachetools import Cache, LRUCache
     cache = LRUCache(maxsize=1000)
     cache['key'] = 'value'
     value = cache['key']
     ```

### 4. **Django Cache Framework**
   - **Описание:** Встроенный кеш в фреймворке Django, который поддерживает различные бэкенды для хранения данных в кеше.
   - **Пример использования:**
     ```python
     from django.core.cache import cache
     cache.set('some_key', 'Some value')
     value = cache.get('some_key')
     ```

### 5. **SQLAlchemy с dogpile.cache**
   - **Описание:** SQLAlchemy — библиотека для работы с базами данных, а `dogpile.cache` — это библиотека для общего кеширования в Python.
   - **Пример использования:**
     ```python
     from sqlalchemy.orm import sessionmaker
     from dogpile.cache.api import CacheRegion
     from dogpile.cache.memory import MemoryCacheBackend

     # Создание региона кеша
     cache_region = CacheRegion(
         name='my_cache_region',
         function_key_generator=lambda *args, **kwargs: 'custom_key',
         backend=MemoryCacheBackend(),
     )

     # Использование в SQLAlchemy
     Session = sessionmaker(bind=engine)
     session = Session()

     @cache_region.cache_on_arguments()
     def get_data_from_database(data_id):
         return session.query(MyModel).get(data_id)
     ```


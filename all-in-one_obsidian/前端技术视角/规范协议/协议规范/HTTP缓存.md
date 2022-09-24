# 缓存方式
## 强缓存
### Expires
### Cache-Control
- no-cache
- max-age
- no-store
- public
- private

## 协商缓存
### Etag
- Etag
- If-None-Match

### Last-Modified
- Last-Modified
- If-Modified-Since


# 实现方式
- Ctrl+F5 强制刷新的时候，会跳过缓存策略
- 使用F5刷新的试试，跳过强缓存，使用协商缓存
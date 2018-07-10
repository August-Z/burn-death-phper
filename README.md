# burn-death-phper
借助 axios 拦截器实现对 json 数据键名的下划线向驼峰的转化

```javascript
// 发送请求的拦截
axios.interceptors.request.use((response) => {
  if (response.method.toUpperCase() === 'GET') {
    response.url = response.url.replace(/[?&][\w]*=/g, (query) => {
      return query.replace(/[A-Z]/g, (el) => `_${el.toLowerCase()}`)
    })
  } else if (/POST|PUT|DELETE/.test(response.method.toUpperCase()) && response.data) {
    response.data = JSON.parse(
      JSON.stringify(response.data).replace(/"\w*":/g, (params) => {
        return params.replace(/[A-Z]/g, (el) => `_${el.toLowerCase()}`)
      })    
    )
  }
  return response
}, (error) => Promise.reject(error))

// 响应请求的拦截
axios.interceptors.response.use((response) => {
  if (response.data) {
    const goodJson = JSON.parse(
      JSON.stringify(response.data).replace(/"\w*":/g, (badJson) => {
        return badJson.replace(/_(\w)/g, ($0, $1) => $1.toUpperCase())
      })
    )
    response.data = goodJson instanceof Array ? [...goodJson] : { ...goodJson }
  }
  return response
}, (error) => Promise.reject(error.response))
```

### 为什么要这样做？
需求源于后端 phper 坚持对于数据库的列名坚持不做任何转化  
导致前端请求 json 数据 key 名均为下划线风格  
有强迫症的 jser 当然不会让驼峰和下划线两种风格共存于自己的代码，哪怕是 api   

   

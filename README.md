# 微博F12代码片段

## 获取微博好友数据
```javascript
(() => {
    const friends = []
    const query = page =>
        fetch(
            `https://weibo.com/ajax/profile/getGroupMembers?list_id=100093073493157&page=${page}`,
            { headers: { 'X-XSRF-TOKEN': (document.cookie.match(/XSRF-TOKEN=([^;$]+)/) || [])[1] || '' } }
        )
            .then(response => response.json())
            .then(body => {
                const { data, msg, ok } = body
                if (data.users.length === 0) return Promise.reject(new Error('finish'))
                if (ok !== 1) return Promise.reject(new Error(msg))
                data.users.forEach(user => friends.push(user))
            })
    const polling = (page = 1) => query(page).then(() => polling(page + 1))

    polling()
        .catch(error => error.message === 'finish' ?
            console.log('获取完成', friends) :
            console.warn('出错了', error.message))
})()
```
### 根据粉丝数量排序
```javascript
friends.sort((a, b) => b.followers_count - a.followers_count).map(friend => {return {'name': friend.name, 'count': friend.followers_count}})
```

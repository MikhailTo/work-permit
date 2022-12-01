1. Ошибка 'digital envelope routines'. 
Решение: 
```
"scripts": {
   "start": "SET NODE_OPTIONS=--openssl-legacy-provider && start",
   "build": "SET NODE_OPTIONS=--openssl-legacy-provider && build"
 },
 ```

 2. Нет возможности закомитить: error: failed to push some refs to 
 Причина: Prettier "Code style issues found in the above file(s). Forgot to run Prettier?"
 Решение: Пока нет, просто удалил prettier. 

3. "v-slot" => # и "v-slot:item.action" => #[`item.actions`] - видимо принятые сокращения, пока приму как данность.
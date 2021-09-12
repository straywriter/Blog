---
title: C++使用Lua
top: false
mathjax: true
categories:
- Lua
---

-----







# a



## Hello World

```cpp
extern "C"
{
#include "lauxlib.h"
#include "lua.h"
#include "lualib.h"

}
#include <string.h>

int main(int argc, char *argv[])
{
    // initialization
    lua_State *L = lua_open();
    luaL_openlibs(L);

    // execute script
    const char lua_script[] = "print('Hello World!')";
    int load_stat = luaL_loadbuffer(L, lua_script, strlen(lua_script), lua_script);
    lua_pcall(L, 0, 0, 0);

    // cleanup
    lua_close(L);
    return 0;
}
```

















## 相关文章

[Using Lua with C++ (Part 1)](https://eliasdaler.wordpress.com/2013/10/11/lua_cpp_binder/)

[Lua written in modern C++ (>= 14)?](https://www.reddit.com/r/cpp/comments/7xohqu/lua_written_in_modern_c_14/)

[BindingCodeToLua](http://lua-users.org/wiki/BindingCodeToLua)
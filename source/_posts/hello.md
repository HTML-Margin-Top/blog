---
title: hello
date: 2017-11-20 17:34:01
tags: 测试
---
你好哇！
<!-- more -->

``` bash
   @Test
    public boolean exist(char[][] board, String word) {
        char[][] xy = {{'a'}};
        Map<String, Character> map = new HashMap<>(16);
        Map<String, Character> next = new HashMap<>(4);

        int xLength = 0;
        int yLength = 0;
        char[] wordSet = word.toCharArray();
        if (board.length == 0 || board[0].length == 0) {
            return false;
        }
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[i].length; j++) {
                if (board[i][j] == wordSet[0]) {
                    xLength = i;
                    yLength = j;
                    map.put(i + "," + j, board[i][j]);
                    next = getNext(board, i, j, board.length, board[i].length, map);
                    break;
                }
            }
        }

        Set<String> result = new HashSet<>(4);
        Set<String> keys = map.keySet();
        Set<String> nextKeys = next.keySet();

        //交集
        result.clear();
        result.addAll(keys);
        result.retainAll(nextKeys);

        nextKeys.removeAll(result);
        if (nextKeys.size() == 0) {
            return  false;
        }
        for (int i = 1 ; i < wordSet.length; i++) {
            for (String key : nextKeys) {
                if (wordSet[1] == next.get(key)) {
                    String[] keyString = key.split(",");
                    xLength= Integer.valueOf(keyString[0]);
                    yLength = Integer.valueOf(keyString[1]);
                    next.clear();
                    map.put(xLength + "," + yLength, board[xLength][yLength]);
                    next = getNext(board, xLength, yLength, board.length, board[xLength].length, map);
                }
            }
        }
        return  false;
    }

    public Map<String, Character> getNext(char[][] board, int x, int y, int xMax, int yMax,  Map<String, Character> thisMap) {
        Map<String, Character> map = new HashMap<>(4);
        int a,b,c,d;
        if (x == 0) {
            a = x + 1;
            b = x + 1;
        } else if (x == xMax) {
            a = x - 1;
            b = x - 1;
        } else {
            a = x - 1;
            b = x + 1;
        }

        if (y == 0) {
            c = y + 1;
            d = y + 1;
        } else if (y == yMax) {
            c = y - 1;
            d = y - 1;
        } else {
            c = y - 1;
            d = y + 1;
        }

        map.put(a + ","+ c, board[a][c]);
        map.put(a + ","+ d, board[a][d]);
        map.put(b + ","+ c, board[b][c]);
        map.put(b + ","+ d, board[b][d]);

        Set<String> result = new HashSet<>(4);
        Set<String> keys = thisMap.keySet();
        Set<String> nextKeys = map.keySet();

        //交集
        result.clear();
        result.addAll(keys);
        result.retainAll(nextKeys);

        nextKeys.removeAll(result);

        return map;
    }
```

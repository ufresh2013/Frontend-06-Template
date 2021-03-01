# 寻路问题
### 地图绘制
绘制一张100*100的地图，一维数组代替二维数组，每个点的坐标(x,y)的数组坐标为100 * y + x
```js
  let map = localStorage.getItem('map') ? JSON.parse(localStorage.getItem('map')) : new Array(10000).fill(0)
  let container = document.getElementById('container')
  let mouseDown = false
  let clear = false
  for (let y = 0; y < 100; y++) {
    for (let x = 0; x < 100; x++) {
      let cell = document.createElement('div')
      cell.classList.add('cell')
      if (map[100 * y + x] === 1) {
        cell.style.backgroundColor = 'black'
      }
      cell.addEventListener('mousemove', e => {
        if (mouseDown) {
          cell.style.backgroundColor = ''
          map[100 * y + x] = 0
        } else {
          cell.style.backgroundColor = 'black'
          map[100 * y + x] = 1
        }
      })
      container.appendChild(cell)
    }
  }
  document.addEventListener('mousedown', e => {
    mouseDown = true
    clear = (e.which === 3) // 点击右键clear为true,清除
  })
  document.addEventListener('mouseup', e => mouseDown = false)
  document.addEventListener('contextmenu', e => e.preventDefault())
```


### 寻路实现
广度优先搜索，使用异步编程中的async, await实现可视化寻路功能
```js
  function wait(time) {
    return new Promise((resolve, reject) => {
      setTimeout(resolve, time)
    })
  }

  // 广度优先搜索
  async function path (map, start, end) {
    const queue = [start]
    async function insert (x, y) {
      if (x < 0 || y < 0 || x >= 100 || y >= 100) return
      if (map[y * 100 + x]) return

      // 通过异步变成可视化寻路算法
      await wait(10)
      container.children[y * 100 + x].style.backgroundColor = 'pink'

      map[y * 100 + x] = 2
      queue.push([x, y])
    }
    while (queue.length) {
      let [x, y] = queue.shift()
      if (x === end[0] && y === end[1]) return true
      await insert(x - 1, y)
      await insert(x, y - 1)
      await insert(x + 1, y)
      await insert(x, y + 1)
    }
    return false
  }

  const findPath = () => { path(map, [0,0], [20,20]) }
```

### A*启发式算法
- 将queue改造成sorted的实例，传入一个distance函数作为compare函数。意在计算每个点与终点质检的距离，并找出每次距离最小值的点。（**可使用最小堆实现**）
- 用table记录每个节点的父节点prev，如果取出的点是终点，回溯pre记录，找到路径
```js
class Sorted {
  constructor (data, compare) {
    this.data = data.slice()
    this.compare = compare || ((a, b) => a - b)
  }

  take () {
    // 保证每次取出最小的
    if (!this.data.length) return
    let min = this.data[0]
    let minIndex = 0
    for (let i = 0; i < this.data.length; i++) {
      if (this.compare(this.data[i], min) < 0) {
        min = this.data[i]
        minIndex = i
      }
    }
    this.data[minIndex] = this.data[this.data.length - 1]
    this.data.pop()
    return min
  }

  give (v) {
    this.data.push(v)
  }
}

async function path(map, start, end) {
  let queue = new Sorted([start], ((a, b) => distance(a) - distance(b)))
  let table = Object.create(map)
  const distance = (point) => {
    return (point[0] - end[0]) ** 2 + (point[1] - end[1]) ** 2
  }

  async function insert(x, y, pre) {
    if (x < 0 || y < 0 || x >= 100 || y >= 100) return
    if (table[y * 100 + x]) return

    await sleep(10)
    container.children[y * 100 + x].style.backgroundColor = 'lightgreen'
    table[y * 100 + x] = pre
    queue.give([x, y])
  }

  while (queue.data.length) {
    let [x, y] = queue.take()

    // 如果取出的点是终点，回溯pre记录，找到路径
    if (x === end[0] && y === end[1]) {
      let path = []
      while (x !== start[0] || y !== start[1]) {
        x = table[y * 100 + x][0]
        y = table[y * 100 + x][1]
        path.push(map[y * 100 + x])
        await sleep(5)
        container.children[y * 100 + x].style.backgroundColor = 'purple'
      }
      return path
    }

    // 如果不是end，把当前4周（8个方位）的点加入队列
    await insert(x - 1, y, [x, y])
    await insert(x , y - 1, [x, y])
    await insert(x + 1, y, [x, y])
    await insert(x, y + 1, [x, y])
    // await insert(x - 1, y - 1, [x, y])
    // await insert(x + 1, y - 1, [x, y])
    // await insert(x - 1, y + 1, [x, y])
    // await insert(x + 1, y + 1, [x, y])
  }

  // 如果队列都找完了，还找不到end，返回false
  return false
}
```
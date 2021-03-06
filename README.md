# asynchronous-mutex-js
### simple example (see examples/simple-mutex.js)
```js
const Mutex = require(`../src/mutex`)
const ColorLog = require("../src/helpers/color-log")
const sleep = require("../src/helpers/sleep")

// setup
const CORUTINES_NUMBER = 3
const Corutines = [...Array(CORUTINES_NUMBER).keys()].map(
  (n) => `corutine ${n}`
)
const sleepTimes = [...Array(CORUTINES_NUMBER).keys()]

// Global variables
const m = new Mutex()
const c = new ColorLog()

const CreateWorker = (sleepTime, workerNumber) => async () => {
  c.log(`Lock mutex`, Corutines[workerNumber])
  await m.Lock()
  c.log(`In CS`, Corutines[workerNumber])
  c.log(`Sleep ${sleepTime}s ...`, Corutines[workerNumber])
  await sleep(sleepTime)
  c.log(`Out CS`, Corutines[workerNumber])
  m.UnLock()
  c.log(`mutex UnLock was invoked :)`, Corutines[workerNumber])
  c.log(`Done`, Corutines[workerNumber])
}

;(async () => {
  const promises = sleepTimes
    // create workers
    .map((sleepTime, workerNumber) => CreateWorker(sleepTime, workerNumber))
    // execute workers
    .map((worker) => worker())
  // wait all tasks
  await Promise.all(promises)
})()
```
### stdout:
```bash
CORUTINE 0: Lock mutex
CORUTINE 1: Lock mutex
CORUTINE 2: Lock mutex
CORUTINE 0: In CS
CORUTINE 0: Sleep 0s ...
CORUTINE 0: Out CS
CORUTINE 0: mutex UnLock was invoked :)
CORUTINE 0: Done
CORUTINE 1: In CS
CORUTINE 1: Sleep 1s ...
CORUTINE 1: Out CS
CORUTINE 1: mutex UnLock was invoked :)
CORUTINE 1: Done
CORUTINE 2: In CS
CORUTINE 2: Sleep 2s ...
CORUTINE 2: Out CS
CORUTINE 2: mutex UnLock was invoked :)
CORUTINE 2: Done
```

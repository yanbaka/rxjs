<template>
  <div class="container">
    <button>button</button>
  </div>
</template>

<script lang="ts">
import {
  onMounted,
  defineComponent,
} from '@nuxtjs/composition-api';
import { Observable, fromEvent, interval, from, Subject } from 'rxjs'
import { map, scan, filter } from "rxjs/operators"

export default defineComponent({
  setup() {
    onMounted(() => {
      const button = document.querySelector('button');
      const clicks = fromEvent(button, 'click')
      clicks.subscribe(x => console.log(x))

      const observable = from([1, 2, 3])
      observable.subscribe({
        next: (x) => console.log(x),
        complete: () => console.log('complete')
      })

      const subscription = interval(1000)
        .pipe(
          filter(x => x%2 === 0),
          scan((acc, curr) => acc + curr, 0),
          map(x => `${x}`)
        )
        .subscribe(x => console.log(x))

      setTimeout(() => {
        subscription.unsubscribe()
        console.log('end')
      }, 10000)

      const observable2 = from([1, 2, 3])
      observable2.subscribe((v) => console.log(`observerA: ${v}`))
      observable2.subscribe((v) => console.log(`observerB: ${v}`))

      const subject = new Subject<number>()
      subject.subscribe((v) => console.log(`A:${v}`))
      const observerA = {
        next:  (x)   => console.log(`B next: ${x}`),
        error: (err) => console.log(`B error: ${err}`),
        complete: () => console.log(`B complete`)
      };
      subject.subscribe(observerA)

      subject.next(1);
      subject.next(2);
      subject.next(3);
      subject.complete();
    })
  }
})
</script>

<style>
</style>

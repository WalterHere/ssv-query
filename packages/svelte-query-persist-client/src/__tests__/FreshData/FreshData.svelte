<script lang="ts">
  import { createQuery } from '@tanstack/svelte-query'
  import { get } from 'svelte/store'
  import { sleep } from '../utils'
  import type { Writable } from 'svelte/store'
  import type { StatusResult } from '../utils'

  export let key: Array<string>
  export let states: Writable<Array<StatusResult<string>>>
  export let fetched: Writable<boolean>

  const state = createQuery({
    queryKey: key,
    queryFn: async () => {
      fetched.set(true)
      await sleep(10)
      return 'fetched'
    },

    staleTime: Infinity,
  })

  let data = get(state).data
  let fetchStatus = get(state).fetchStatus
  state.subscribe((s) => {
    states.update((prev) => [
      ...prev,
      { status: s.status, data: s.data, fetchStatus: s.fetchStatus },
    ])
    data = s.data
    fetchStatus = s.fetchStatus
  })
</script>

<div>
  <h1>data: {data ?? 'null'}</h1>
  <h2>fetchStatus: {fetchStatus}</h2>
</div>

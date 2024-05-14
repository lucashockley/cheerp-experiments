<script lang="ts">
  import { onMount } from "svelte";

  let cxConsole: HTMLPreElement;

  onMount(async () => {
    const cx = await CheerpXApp.create({
      devices: [
        {
          name: "block1",
          type: "block",
          url: "https://disks.webvm.io/debian_large_20230522_5044875331.ext2",
        },
      ],
      mounts: [
        { type: "ext2", dev: "block1", path: "/" },
        { type: "cheerpOS", dev: "/app", path: "/app" },
        { type: "cheerpOS", dev: "/str", path: "/data" },
        { type: "devs", dev: "", path: "/dev" },
      ],
    });

    cx.setConsole(cxConsole);

    await cx.run("/bin/bash", ["--login"], {
      env: [
        "HOME=/home/user",
        "USER=user",
        "SHELL=/bin/bash",
        "EDITOR=vim",
        "LANG=en_US.UTF-8",
        "LC_ALL=C",
      ],
      cwd: "/home/user",
      uid: 1000,
      gid: 1000,
    });
  });
</script>

<svelte:head>
  <script
    src="https://cheerpxdemos.leaningtech.com/publicdeploy/20240501_137/cx.js"
  ></script>
</svelte:head>

<pre bind:this={cxConsole}></pre>

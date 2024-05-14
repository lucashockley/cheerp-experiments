<script lang="ts">
  import { onMount } from "svelte";

  let cxConsole: HTMLPreElement;
  let fileInput: HTMLInputElement;

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

  async function handleFileUpload() {
    if (!fileInput?.files) return;

    for (const file of fileInput.files) {
      const data = await file.arrayBuffer();
      const a = new Uint8Array(data);

      cheerpOSAddStringFile(`/str/${file.name}`, a);
    }
  }
</script>

<svelte:head>
  <script
    src="https://cheerpxdemos.leaningtech.com/publicdeploy/20240501_137/cx.js"
  ></script>
</svelte:head>

<label for="file-upload">Upload Files to the Filesystem</label>
<input
  bind:this={fileInput}
  on:change={handleFileUpload}
  type="file"
  id="file-upload"
/>

<pre bind:this={cxConsole}></pre>

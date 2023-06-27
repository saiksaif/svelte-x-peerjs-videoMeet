<script>
  import {Peer} from 'peerjs';
  var peer = new Peer();

  let codeid = '';
  let videocurrent;
  let videoel;
  let youid = '';

  // Get youID
  peer.on('open', (id)=>{
    youid = id;
    console.log(id);
  });
  // If error getting ID
  peer.on('error', (id)=>{
    console.log("error id" + id);
  })

  peer.on('connection', (conn)=>{
    console.log("Message...");
    
    conn.on('data', (data)=>{
      console.log("new data " + data);
    });

    conn.on('open', ()=>{
      console.log("new message");
    });
  })

  // Handle Connection
  peer.on('call', async(call)=>{
    await navigator.mediaDevices.getUserMedia({
      audio: true,
      video: true
    }).then((stream)=>{
      call.answer(stream);
      call.on('stream', renderYourWebcam);
      videocurrent.srcObject = stream;
      videocurrent.play();
    }).catch((err)=>console.log(err));
  });

  // Render webcam here
  let renderYourWebcam = (stream)=>{
    console.log(stream);
    videoel.srcObject = stream;
    videoel.play();
  };
</script>

<main>
  <h2>Svelte x Peer.js Video Meeting Platform</h2> <hr>

  Your ID = {youid}
  <br> <br>

  Enter ID to Connect:
  <input type="text" bind:value={codeid}>
  <br> <br>

  <!-- Connect Button -->
  <button on:click={
    async()=>{
      var conn = peer.connect(codeid);
      conn.on('data', (data)=>{
        console.log("new data " + data);
      })
      conn.on('open', function() {
        conn.send('Hi');
      })
      // Open WebCam
      await navigator.mediaDevices.getUserMedia({
        video: true,
        audio: true
      }).then(stream=>{
        let call = peer.call(codeid, stream);
        videocurrent.srcObject = stream;
        videocurrent.play();
        call.on('stream', renderYourWebcam);
      }).catch(err=>console.log("Have error " + err))
    }
  }>Connect</button>

  <hr>
  
  <div class="contain">
    <!-- Friend Video -->
    <div>
      <h3>Friend Video</h3>

      <video class="fren" bind:this={videoel} height="300" width="300">
        <track kind="captions" src="" />
      </video>
    </div>
    
    <br>
    
    <!-- Your Video -->
    <div>
      <h3>My Video</h3>
      
      <video class="me" bind:this={videocurrent} height="300" width="300">
        <track kind="captions" src="" />
      </video>
    </div>
  </div>
  
</main>

<style>
  .contain {
    display: flex;
  }
  .fren {background-color: rgb(176, 0, 0);
    border: 1px solid rgb(176, 0, 0);
  }
  .me {
    background-color: rgb(4, 4, 195);
    border: 1px solid rgb(4, 4, 195);
  }
</style>

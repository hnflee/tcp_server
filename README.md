#rust tcp server frame.

# Examples Echo
``` rust
#![feature(async_closure)]
use tcpserver::tokio;
use tokio::io::AsyncReadExt;
use tcpserver::XBWrite;
use tcpserver::Builder;

#[tokio::main]
async fn main()->Result<(),Box<dyn Error>> {
    let tcpserver = Builder::new("0.0.0.0:8998")
        .set_connect_event(|addr| {
            println!("{:?} connect", addr);
            true
        }).set_input_event(async move |mut reader, peer,_| {
        let mut buff = [0; 4096];
        while let Ok(len) = reader.read(&mut buff).await  {
            if len==0{
                break;
            }
            println!("{:?}",&buff[..len]);
            peer.send(buff[..len].to_vec()).await.unwrap();
        }
        println!("{:?} disconnect",peer.addr());
    }).build().await;

    tcpserver.start(()).await?;
    Ok(())
}
```

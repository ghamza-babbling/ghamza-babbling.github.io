---
title: "GStreamer First App in Rust"
taxonomies:
  tags: ["gstreamer", "programming", "rust"]
---

## Setup

I'm on macOS and set up GStreamer by [downloading GStreamer binaries](https://docs.rs/gstreamer/latest/gstreamer/#gstreamer-binaries). I took this option instead of homebrew because Gstreamer said it's broken :/.

> We recommend using the official GStreamer binaries over Homebrew, especially as GStreamer in Homebrew is currently broken.

Then, I created a system link for dynamic libs to the local libs.

```bash
sudo ln -s /Library/Frameworks/GStreamer.framework/Versions/1.0/lib/*.dylib /usr/local/lib/
```

Created a new rust bin project with `gstreamer` and `anyhow`.

```bash
cargo new --bin
```

```toml
[dependencies]
anyhow = "1.0.75"
gst = { package = "gstreamer", version = "0.21.0" }
```

## First GStreamer App

I have been reading GStreamer's application development manual.

[On this page, they talk about building your first GStreamer app in C.](https://gstreamer.freedesktop.org/documentation/application-development/basics/helloworld.html?gi-language=c#)

I translated that to Rust and removed the parts regarding lifetimes, like handling bus events.

```rust
use gst::glib;
use gst::prelude::ElementExt;
use gst::prelude::ElementExtManual;
use gst::prelude::GstBinExtManual;
use gst::traits::GstBinExt;
use gst::traits::GstObjectExt;

fn main() -> anyhow::Result<()> {
    // gst_init (&argc, &argv);
    gst::init()?;

    // loop = g_main_loop_new (NULL, FALSE);
    let main_loop = glib::MainLoop::new(None, false);

    // pipeline = gst_pipeline_new ("audio-player");
    let pipeline = gst::Pipeline::with_name("audio-player");

    // source = gst_element_factory_make ("filesrc", "file-source");
    let source = gst::ElementFactory::make("filesrc")
        .name("file-source")
        // g_object_set (G_OBJECT (source), "location", "example.ogg", NULL);
        .property("location", "example.ogg")
        .build()?;

    // demuxer = gst_element_factory_make ("oggdemux", "ogg-demuxer");
    let demuxer = gst::ElementFactory::make("oggdemux")
        .name("ogg-demuxer")
        .build()?;

    // decoder = gst_element_factory_make ("vorbisdec", "vorbis-decoder");
    let decoder = gst::ElementFactory::make("vorbisdec")
        .name("vorbis-decoder")
        .build()?;

    // conv = gst_element_factory_make ("audioconvert", "converter");
    let conv = gst::ElementFactory::make("audioconvert")
        .name("converter")
        .build()?;

    // sink = gst_element_factory_make ("autoaudiosink", "audio-output");
    let sink = gst::ElementFactory::make("autoaudiosink")
        .name("audio-output")
        .build()?;

    // gst_bin_add_many (GST_BIN (pipeline), source, demuxer, decoder, conv, sink, NULL);
    pipeline.add_many([&source, &demuxer, &decoder, &conv, &sink])?;

    // gst_element_link (source, demuxer);
    source.link(&demuxer)?;

    // gst_element_link_many (decoder, conv, sink, NULL);
    gst::Element::link_many([&decoder, &conv, &sink])?;

    // g_signal_connect (demuxer, "pad-added", G_CALLBACK (on_pad_added), decoder);
    demuxer.connect_pad_added(move |elem, _pad| {
        _ = elem.link(&decoder);
    });

    // gst_element_set_state (pipeline, GST_STATE_PLAYING);
    pipeline.set_state(gst::State::Playing)?;

    // g_main_loop_run (loop);
    main_loop.run();

    // gst_element_set_state (pipeline, GST_STATE_NULL);
    pipeline.set_state(gst::State::Null)?;

    Ok(())
}
```

![pipeline](https://gstreamer.freedesktop.org/documentation/application-development/basics/images/hello-world.png)

I didn't expect it just to work 😅. I'll take that :D.

Now, I can [write a CLI app](https://blog.ghamza.dev/posts/rust-cli/) leverage the GStreamer tool.

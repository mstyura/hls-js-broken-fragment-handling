# hls-js-broken-fragment-handling

0. Steps to reproduce locally
1. Open this repository in VSCode dev container
2. Run `npm install` within dev container;
3. Run `./node_modules/.bin/http-server -S -C cert.pem -p 18098 --cors` to start http server;
4. Open `https://127.0.0.1:18098/` in browser and trust self-signed certificate;
4. Open `https://hlsjs.video-dev.org/demo/` in browser [direct link with configuration](https://hlsjs.video-dev.org/demo/?src=https%3A%2F%2F127.0.0.1%3A18098%2Flocal.m3u8&demoConfig=eyJlbmFibGVTdHJlYW1pbmciOnRydWUsImF1dG9SZWNvdmVyRXJyb3IiOnRydWUsInN0b3BPblN0YWxsIjpmYWxzZSwiZHVtcGZNUDQiOmZhbHNlLCJsZXZlbENhcHBpbmciOi0xLCJsaW1pdE1ldHJpY3MiOi0xfQ==)
5. Observe browser complaining with decoding error:

Chrome:
```
6.322 | Media element detached
6.327 | Loading https://localhost:18098/local.m3u8
6.328 | Loading manifest and attaching video element...
6.335 | 1 quality levels found
6.336 | Manifest successfully loaded
6.348 | Media element attached, trying to recover media error.
9.327 | Media element detached
9.329 | The video playback was aborted due to a corruption problem or because the video used features your browser did not support - PIPELINE_ERROR_DECODE: Error Domain=NSOSStatusErrorDomain Code=-12909 "(null)" (-12909): VTDecompressionOutputCallback
9.359 | Media element attached, trying to swap audio codec and recover media error.
9.669 | Media element detached
9.67 | The video playback was aborted due to a corruption problem or because the video used features your browser did not support - PIPELINE_ERROR_DECODE: Error Domain=NSOSStatusErrorDomain Code=-12909 "(null)" (-12909): VTDecompressionOutputCallback
9.692 | Media element attached, cannot recover. Last media error recovery failed.
9.921 | The video playback was aborted due to a corruption problem or because the video used features your browser did not support - PIPELINE_ERROR_DECODE: Error Domain=NSOSStatusErrorDomain Code=-12909 "(null)" (-12909): VTDecompressionOutputCallback
```
Firefox:
```
8.016 | Media element detached
8.018 | Loading https://localhost:18098/local.m3u8
8.019 | Loading manifest and attaching video element...
8.024 | Media element attached
8.026 | 1 quality levels found
8.026 | Manifest successfully loaded, trying to recover media error.
10.768 | Media element detached
10.772 | The video playback was aborted due to a corruption problem or because the video used features your browser did not support
10.781 | Media element attached, trying to swap audio codec and recover media error.
11.052 | Media element detached
11.053 | The video playback was aborted due to a corruption problem or because the video used features your browser did not support
11.059 | Media element attached, cannot recover. Last media error recovery failed.
11.264 | The video playback was aborted due to a corruption problem or because the video used features your browser did not support
```
Safari:
```
17.08 | Media element detached
17.081 | Loading https://127.0.0.1:18098/local.m3u8
17.083 | Loading manifest and attaching video element...
17.09 | Media element attached
17.144 | 1 quality levels found
17.145 | Manifest successfully loaded, trying to recover media error.
20.567 | Media element detached
20.573 | The video playback was aborted due to a corruption problem or because the video used features your browser did not support - Media failed to decode
20.588 | Media element attached, trying to swap audio codec and recover media error.
21.072 | Media element detached
21.078 | The video playback was aborted due to a corruption problem or because the video used features your browser did not support - Media failed to decode
21.096 | Media element attached, cannot recover. Last media error recovery failed.
21.358 | The video playback was aborted due to a corruption problem or because the video used features your browser did not support - Media failed to decode, cannot recover. Last media error recovery failed.
```

FFplay `ffplay -v debug -live_start_index 0 local-ffplay.m3u8`
```
ffplay -v debug -live_start_index 0 ffplay.m3u8
ffplay version 7.1 Copyright (c) 2003-2024 the FFmpeg developers
  built with Apple clang version 16.0.0 (clang-1600.0.26.4)
  configuration: --prefix=/opt/homebrew/Cellar/ffmpeg/7.1_4 --enable-shared --enable-pthreads --enable-version3 --cc=clang --host-cflags= --host-ldflags='-Wl,-ld_classic' --enable-ffplay --enable-gnutls --enable-gpl --enable-libaom --enable-libaribb24 --enable-libbluray --enable-libdav1d --enable-libharfbuzz --enable-libjxl --enable-libmp3lame --enable-libopus --enable-librav1e --enable-librist --enable-librubberband --enable-libsnappy --enable-libsrt --enable-libssh --enable-libsvtav1 --enable-libtesseract --enable-libtheora --enable-libvidstab --enable-libvmaf --enable-libvorbis --enable-libvpx --enable-libwebp --enable-libx264 --enable-libx265 --enable-libxml2 --enable-libxvid --enable-lzma --enable-libfontconfig --enable-libfreetype --enable-frei0r --enable-libass --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libopenjpeg --enable-libspeex --enable-libsoxr --enable-libzmq --enable-libzimg --disable-libjack --disable-indev=jack --enable-videotoolbox --enable-audiotoolbox --enable-neon
  libavutil      59. 39.100 / 59. 39.100
  libavcodec     61. 19.100 / 61. 19.100
  libavformat    61.  7.100 / 61.  7.100
  libavdevice    61.  3.100 / 61.  3.100
  libavfilter    10.  4.100 / 10.  4.100
  libswscale      8.  3.100 /  8.  3.100
  libswresample   5.  3.100 /  5.  3.100
  libpostproc    58.  3.100 / 58.  3.100
Initialized metal renderer.
[AVFormatContext @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading
[file @ 0x600000e160d0] Setting default whitelist 'file,crypto,data'
[hls @ 0x125e0fa20] Format hls probed with size=2048 and score=100
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[hls @ 0x125e0fa20] HLS request for url '326.ts', offset 0, playlist 0
[hls @ 0x125e0fa20] Opening '326.ts' for reading
Format mpegts probed with size=2048 and score=50
[mpegts @ 0x125f043d0] stream=0 stream_type=1b pid=100 prog_reg_desc=
[mpegts @ 0x125f043d0] stream=1 stream_type=f pid=101 prog_reg_desc=
[hls @ 0x125e0fa20] Before avformat_find_stream_info() pos: 507 bytes read:507 seeks:0 nb_streams:2
Transform tree:
    mdct_pfa_3xM_inv_float_c - type: mdct_float, len: 96, factors[2]: [3, any], flags: [unaligned, out_of_place, inv_only]
        fft16_ns_float_neon - type: fft_float, len: 16, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_pfa_15xM_inv_float_c - type: mdct_float, len: 120, factors[2]: [15, any], flags: [unaligned, out_of_place, inv_only]
        fft4_fwd_float_neon - type: fft_float, len: 4, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_inv_float_c - type: mdct_float, len: 128, factors[2]: [2, any], flags: [unaligned, out_of_place, inv_only]
        fft_sr_ns_float_neon - type: fft_float, len: 64, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_pfa_15xM_inv_float_c - type: mdct_float, len: 480, factors[2]: [15, any], flags: [unaligned, out_of_place, inv_only]
        fft16_ns_float_neon - type: fft_float, len: 16, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_inv_float_c - type: mdct_float, len: 512, factors[2]: [2, any], flags: [unaligned, out_of_place, inv_only]
        fft_sr_ns_float_neon - type: fft_float, len: 256, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_pfa_3xM_inv_float_c - type: mdct_float, len: 768, factors[2]: [3, any], flags: [unaligned, out_of_place, inv_only]
        fft_sr_ns_float_neon - type: fft_float, len: 128, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_pfa_15xM_inv_float_c - type: mdct_float, len: 960, factors[2]: [15, any], flags: [unaligned, out_of_place, inv_only]
        fft32_ns_float_neon - type: fft_float, len: 32, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_inv_float_c - type: mdct_float, len: 1024, factors[2]: [2, any], flags: [unaligned, out_of_place, inv_only]
        fft_sr_ns_float_neon - type: fft_float, len: 512, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_fwd_float_c - type: mdct_float, len: 1024, factors[2]: [2, any], flags: [unaligned, out_of_place, fwd_only]
        fft_sr_ns_float_neon - type: fft_float, len: 512, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
[mpegts @ 0x125f043d0] All programs have pmt, headers found
[mpegts @ 0x125f043d0] probing stream 1 pp:2500
[mpegts @ 0x125f043d0] Probe with size=166, packets=1 detected aac with score=1
[mpegts @ 0x125f043d0] probing stream 1 pp:2499
[mpegts @ 0x125f043d0] Probe with size=329, packets=2 detected aac with score=1
[mpegts @ 0x125f043d0] probing stream 1 pp:2498
[mpegts @ 0x125f043d0] probing stream 1 pp:2497
[mpegts @ 0x125f043d0] Probe with size=646, packets=4 detected aac with score=51
[mpegts @ 0x125f043d0] probed stream 1
Transform tree:
    mdct_inv_float_c - type: mdct_float, len: 64, factors[2]: [2, any], flags: [unaligned, out_of_place, inv_only]
        fft32_ns_float_neon - type: fft_float, len: 32, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_inv_float_c - type: mdct_float, len: 64, factors[2]: [2, any], flags: [unaligned, out_of_place, inv_only]
        fft32_ns_float_neon - type: fft_float, len: 32, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
[extract_extradata @ 0x600000609590] nal_unit_type: 9(AUD), nal_ref_idc: 0
[extract_extradata @ 0x600000609590] nal_unit_type: 7(SPS), nal_ref_idc: 1
[extract_extradata @ 0x600000609590] nal_unit_type: 8(PPS), nal_ref_idc: 1
[extract_extradata @ 0x600000609590] nal_unit_type: 5(IDR), nal_ref_idc: 1
[h264 @ 0x10bd11ee0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10bd11ee0] nal_unit_type: 7(SPS), nal_ref_idc: 1
[h264 @ 0x10bd11ee0] nal_unit_type: 8(PPS), nal_ref_idc: 1
[h264 @ 0x10bd11ee0] nal_unit_type: 5(IDR), nal_ref_idc: 1
[h264 @ 0x10bd11ee0] Format yuv420p chosen by get_format().
[h264 @ 0x10bd11ee0] Reinit context to 368x640, pix_fmt: yuv420p
[h264 @ 0x10bd11ee0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10bd11ee0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10bd11ee0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10bd11ee0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10bd11ee0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10bd11ee0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10bd11ee0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10bd11ee0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10bd11ee0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10bd11ee0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10bd11ee0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10bd11ee0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[hls @ 0x125e0fa20] All info found
[hls @ 0x125e0fa20] rfps: 29.000000 0.015887
[hls @ 0x125e0fa20] rfps: 29.083333 0.012074
[hls @ 0x125e0fa20] rfps: 29.166667 0.008786
    Last message repeated 1 times
[hls @ 0x125e0fa20] rfps: 29.250000 0.006024
    Last message repeated 1 times
[hls @ 0x125e0fa20] rfps: 29.333333 0.003788
[hls @ 0x125e0fa20] rfps: 29.416667 0.002077
[hls @ 0x125e0fa20] rfps: 29.500000 0.000891
    Last message repeated 1 times
[hls @ 0x125e0fa20] rfps: 29.583333 0.000230
    Last message repeated 1 times
[hls @ 0x125e0fa20] rfps: 29.666667 0.000095
    Last message repeated 1 times
[hls @ 0x125e0fa20] rfps: 29.750000 0.000486
[hls @ 0x125e0fa20] rfps: 29.833333 0.001402
    Last message repeated 1 times
[hls @ 0x125e0fa20] rfps: 29.916667 0.002843
    Last message repeated 1 times
[hls @ 0x125e0fa20] rfps: 30.000000 0.004810
    Last message repeated 1 times
[hls @ 0x125e0fa20] rfps: 59.000000 0.003563
    Last message repeated 1 times
[hls @ 0x125e0fa20] rfps: 60.000000 0.019240
    Last message repeated 1 times
[hls @ 0x125e0fa20] rfps: 29.970030 0.004042
    Last message repeated 1 times
[hls @ 0x125e0fa20] rfps: 59.940060 0.016169
[hls @ 0x125e0fa20] Setting avg frame rate based on r frame rate
[hls @ 0x125e0fa20] After avformat_find_stream_info() pos: 507 bytes read:507 seeks:0 frames:52
Input #0, hls, from 'ffplay.m3u8':
  Duration: N/A, start: 910.183000, bitrate: N/A
  Program 0 
    Metadata:
      variant_bitrate : 0
  Stream #0:0, 21, 1/90000: Video: h264 (Main), 1 reference frame ([27][0][0][0] / 0x001B), yuv420p, 360x640, 0/1, 29.67 fps, 29.67 tbr, 90k tbn
      Metadata:
        variant_bitrate : 0
  Stream #0:1, 31, 1/90000: Audio: aac (LC) ([15][0][0][0] / 0x000F), 44100 Hz, mono, fltp
      Metadata:
        variant_bitrate : 0
Transform tree:
    mdct_inv_float_c - type: mdct_float, len: 64, factors[2]: [2, any], flags: [unaligned, out_of_place, inv_only]
        fft32_ns_float_neon - type: fft_float, len: 32, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_inv_float_c - type: mdct_float, len: 64, factors[2]: [2, any], flags: [unaligned, out_of_place, inv_only]
        fft32_ns_float_neon - type: fft_float, len: 32, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_pfa_3xM_inv_float_c - type: mdct_float, len: 96, factors[2]: [3, any], flags: [unaligned, out_of_place, inv_only]
        fft16_ns_float_neon - type: fft_float, len: 16, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_pfa_15xM_inv_float_c - type: mdct_float, len: 120, factors[2]: [15, any], flags: [unaligned, out_of_place, inv_only]
        fft4_fwd_float_neon - type: fft_float, len: 4, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_inv_float_c - type: mdct_float, len: 128, factors[2]: [2, any], flags: [unaligned, out_of_place, inv_only]
        fft_sr_ns_float_neon - type: fft_float, len: 64, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_pfa_15xM_inv_float_c - type: mdct_float, len: 480, factors[2]: [15, any], flags: [unaligned, out_of_place, inv_only]
        fft16_ns_float_neon - type: fft_float, len: 16, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_inv_float_c - type: mdct_float, len: 512, factors[2]: [2, any], flags: [unaligned, out_of_place, inv_only]
        fft_sr_ns_float_neon - type: fft_float, len: 256, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_pfa_3xM_inv_float_c - type: mdct_float, len: 768, factors[2]: [3, any], flags: [unaligned, out_of_place, inv_only]
        fft_sr_ns_float_neon - type: fft_float, len: 128, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_pfa_15xM_inv_float_c - type: mdct_float, len: 960, factors[2]: [15, any], flags: [unaligned, out_of_place, inv_only]
        fft32_ns_float_neon - type: fft_float, len: 32, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_inv_float_c - type: mdct_float, len: 1024, factors[2]: [2, any], flags: [unaligned, out_of_place, inv_only]
        fft_sr_ns_float_neon - type: fft_float, len: 512, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
Transform tree:
    mdct_fwd_float_c - type: mdct_float, len: 1024, factors[2]: [2, any], flags: [unaligned, out_of_place, fwd_only]
        fft_sr_ns_float_neon - type: fft_float, len: 512, factor: 2, flags: [aligned, inplace, out_of_place, preshuf]
detected 12 logical cores
[ffplay_abuffer @ 0x600001218210] Setting 'sample_rate' to value '44100'
[ffplay_abuffer @ 0x600001218210] Setting 'sample_fmt' to value 'fltp'
[ffplay_abuffer @ 0x600001218210] Setting 'time_base' to value '1/44100'
[ffplay_abuffer @ 0x600001218210] Setting 'channel_layout' to value 'mono'
[ffplay_abuffer @ 0x600001218210] tb:1/44100 samplefmt:fltp samplerate:44100 chlayout:mono
[ffplay_abuffersink @ 0x600001218160] auto-inserting filter 'auto_aresample_0' between the filter 'ffplay_abuffer' and the filter 'ffplay_abuffersink'
[AVFilterGraph @ 0x600000e2adf0] query_formats: 2 queried, 0 merged, 4 already done, 0 delayed
[auto_aresample_0 @ 0x600001210000] [SWR @ 0x1500c8000] Using fltp internally between filters
[auto_aresample_0 @ 0x600001210000] ch:1 chl:mono fmt:fltp r:44100Hz -> ch:1 chl:mono fmt:s16 r:44100Hz
[h264 @ 0x10b815f60] nal_unit_type: 7(SPS), nal_ref_idc: 1
[h264 @ 0x10b815f60] nal_unit_type: 8(PPS), nal_ref_idc: 1
Audio frame changed from rate:44100 ch:1 fmt:fltp layout:mono serial:-1 to rate:44100 ch:1 fmt:fltp layout:mono serial:1
[AVIOContext @ 0x10bc0e560] Statistics: 60348 bytes read, 0 seeks
[hls @ 0x125e0fa20] HLS request for url '327.ts', offset 0, playlist 0
[hls @ 0x125e0fa20] Opening '327.ts' for reading
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b815f60] nal_unit_type: 7(SPS), nal_ref_idc: 1
[h264 @ 0x10b815f60] nal_unit_type: 8(PPS), nal_ref_idc: 1
[h264 @ 0x10b815f60] nal_unit_type: 5(IDR), nal_ref_idc: 1
[h264 @ 0x10b815f60] Format yuv420p chosen by get_format().
[h264 @ 0x10b815f60] Reinit context to 368x640, pix_fmt: yuv420p
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b81e9c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[ffplay_abuffer @ 0x600001210000] Setting 'sample_rate' to value '44100'
[ffplay_abuffer @ 0x600001210000] Setting 'sample_fmt' to value 'fltp'
[ffplay_abuffer @ 0x600001210000] Setting 'time_base' to value '1/44100'
[ffplay_abuffer @ 0x600001210000] Setting 'channel_layout' to value 'mono'
[ffplay_abuffer @ 0x600001210000] tb:1/44100 samplefmt:fltp samplerate:44100 chlayout:mono
[ffplay_abuffersink @ 0x600001210c60] auto-inserting filter 'auto_aresample_0' between the filter 'ffplay_abuffer' and the filter 'ffplay_abuffersink'
[AVFilterGraph @ 0x600000e326f0] query_formats: 2 queried, 0 merged, 3 already done, 0 delayed
[auto_aresample_0 @ 0x600001210d10] [SWR @ 0x158028000] Using fltp internally between filters
[auto_aresample_0 @ 0x600001210d10] ch:1 chl:mono fmt:fltp r:44100Hz -> ch:1 chl:mono fmt:s16 r:44100Hz
[mpegts @ 0x125f043d0] Continuity check failed for pid 0 expected 1 got 0
[mpegts @ 0x125f043d0] Continuity check failed for pid 4097 expected 1 got 0
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b838640] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b840fc0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b840fc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b849940] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b849940] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8522c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b8522c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b85ac40] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b85ac40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8635c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b8635c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b86bf40] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b86bf40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8748c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b8748c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
Video frame changed from size:0x0 format:none serial:-1 to size:360x640 format:yuv420p serial:1
[h264 @ 0x10b87d240] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b87d240] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[ffplay_buffer @ 0x600001215600] Setting 'video_size' to value '360x640'
[ffplay_buffer @ 0x600001215600] Setting 'pix_fmt' to value '0'
[ffplay_buffer @ 0x600001215600] Setting 'time_base' to value '1/90000'
[ffplay_buffer @ 0x600001215600] Setting 'pixel_aspect' to value '0/1'
[ffplay_buffer @ 0x600001215600] Setting 'colorspace' to value '2'
[ffplay_buffer @ 0x600001215600] Setting 'range' to value '0'
[ffplay_buffer @ 0x600001215600] Setting 'frame_rate' to value '89/3'
[ffplay_buffer @ 0x600001215600] w:360 h:640 pixfmt:yuv420p tb:1/90000 fr:89/3 sar:0/1 csp:unknown range:unknown
[auto_scale_0 @ 0x600001215760] w:iw h:ih flags:'' interl:0
[ffplay_buffersink @ 0x6000012156b0] auto-inserting filter 'auto_scale_0' between the filter 'ffplay_buffer' and the filter 'ffplay_buffersink'
[AVFilterGraph @ 0x600000e003f0] query_formats: 2 queried, 0 merged, 3 already done, 0 delayed
[swscaler @ 0x158cb8000] [swscaler @ 0x158cc8000] YUV color matrix differs for YUV->YUV, using intermediate RGB to convert
[swscaler @ 0x158e48000] No accelerated colorspace conversion found from yuv420p to bgr24.
[swscaler @ 0x158cb8000] [swscaler @ 0x158cd8000] YUV color matrix differs for YUV->YUV, using intermediate RGB to convert
[swscaler @ 0x158f18000] No accelerated colorspace conversion found from yuv420p to bgr24.
[swscaler @ 0x158cb8000] [swscaler @ 0x158ce8000] YUV color matrix differs for YUV->YUV, using intermediate RGB to convert
[swscaler @ 0x158fe8000] No accelerated colorspace conversion found from yuv420p to bgr24.
[swscaler @ 0x158cb8000] [swscaler @ 0x158cf8000] YUV color matrix differs for YUV->YUV, using intermediate RGB to convert
[swscaler @ 0x1590b8000] No accelerated colorspace conversion found from yuv420p to bgr24.
[swscaler @ 0x158cb8000] [swscaler @ 0x158d08000] YUV color matrix differs for YUV->YUV, using intermediate RGB to convert
[swscaler @ 0x159188000] No accelerated colorspace conversion found from yuv420p to bgr24.
[swscaler @ 0x158cb8000] [swscaler @ 0x158d18000] YUV color matrix differs for YUV->YUV, using intermediate RGB to convert
[swscaler @ 0x170178000] No accelerated colorspace conversion found from yuv420p to bgr24.
[swscaler @ 0x158cb8000] [swscaler @ 0x158d28000] YUV color matrix differs for YUV->YUV, using intermediate RGB to convert
[swscaler @ 0x170198000] No accelerated colorspace conversion found from yuv420p to bgr24.
[swscaler @ 0x158cb8000] [swscaler @ 0x158d38000] YUV color matrix differs for YUV->YUV, using intermediate RGB to convert
[swscaler @ 0x1703c8000] No accelerated colorspace conversion found from yuv420p to bgr24.
[swscaler @ 0x158cb8000] [swscaler @ 0x158d48000] YUV color matrix differs for YUV->YUV, using intermediate RGB to convert
[swscaler @ 0x170498000] No accelerated colorspace conversion found from yuv420p to bgr24.
[swscaler @ 0x158cb8000] [swscaler @ 0x158d58000] YUV color matrix differs for YUV->YUV, using intermediate RGB to convert
[swscaler @ 0x1488a8000] No accelerated colorspace conversion found from yuv420p to bgr24.
[swscaler @ 0x158cb8000] [swscaler @ 0x158d68000] YUV color matrix differs for YUV->YUV, using intermediate RGB to convert
[swscaler @ 0x148978000] No accelerated colorspace conversion found from yuv420p to bgr24.
[swscaler @ 0x158cb8000] [swscaler @ 0x158d78000] YUV color matrix differs for YUV->YUV, using intermediate RGB to convert
[swscaler @ 0x148a48000] No accelerated colorspace conversion found from yuv420p to bgr24.
[swscaler @ 0x158cb8000] [swscaler @ 0x158d88000] YUV color matrix differs for YUV->YUV, using intermediate RGB to convert
[swscaler @ 0x148b18000] No accelerated colorspace conversion found from yuv420p to bgr24.
[auto_scale_0 @ 0x600001215760] w:360 h:640 fmt:yuv420p csp:unknown range:unknown sar:0/1 -> w:360 h:640 fmt:yuv420p csp:bt709 range:unknown sar:0/1 flags:0x00000004
[auto_scale_0 @ 0x600001215760] [framesync @ 0x10bd17798] Selected 1/90000 time base
[auto_scale_0 @ 0x600001215760] [framesync @ 0x10bd17798] Sync level 1
[ffplay_buffer @ 0x600001215600] video frame properties congruent with link at pts_time: 910.183
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b815f60] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b81e9c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
Created 360x640 texture with SDL_PIXELFORMAT_IYUV.
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b838640] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b840fc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b840fc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b849940] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b849940] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8522c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8522c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b85ac40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b85ac40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8635c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8635c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b86bf40] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b86bf40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8748c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8748c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b87d240] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b87d240] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b815f60] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b81e9c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[AVIOContext @ 0x10bc1fc70] Statistics: 54896 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x127804080] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] HLS request for url '328.ts', offset 0, playlist 0
[hls @ 0x125e0fa20] Opening '328.ts' for reading
[mpegts @ 0x125f043d0] Continuity check failed for pid 0 expected 1 got 0
[mpegts @ 0x125f043d0] Continuity check failed for pid 4097 expected 1 got 0
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b838640] nal_unit_type: 7(SPS), nal_ref_idc: 1
[h264 @ 0x10b838640] nal_unit_type: 8(PPS), nal_ref_idc: 1
[h264 @ 0x10b838640] nal_unit_type: 5(IDR), nal_ref_idc: 1
[h264 @ 0x10b840fc0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b840fc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b849940] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b849940] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8522c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8522c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b85ac40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b85ac40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8635c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8635c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b86bf40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b86bf40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8748c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8748c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b87d240] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b87d240] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b815f60] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b81e9c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b838640] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b840fc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b840fc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b849940] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b849940] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8522c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8522c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b85ac40] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b85ac40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8635c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8635c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b86bf40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b86bf40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8748c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b8748c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b87d240] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b87d240] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b815f60] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b81e9c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b838640] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b840fc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b840fc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b849940] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b849940] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8522c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8522c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[AVIOContext @ 0x127804080] Statistics: 68808 bytes read, 0 seeks
[hls @ 0x125e0fa20] HLS request for url '329.ts', offset 0, playlist 0
[hls @ 0x125e0fa20] Opening '329.ts' for reading
[mpegts @ 0x125f043d0] Continuity check failed for pid 0 expected 1 got 0
[mpegts @ 0x125f043d0] Continuity check failed for pid 4097 expected 1 got 0
[h264 @ 0x10b85ac40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b85ac40] nal_unit_type: 7(SPS), nal_ref_idc: 1
[h264 @ 0x10b85ac40] nal_unit_type: 8(PPS), nal_ref_idc: 1
[h264 @ 0x10b85ac40] nal_unit_type: 5(IDR), nal_ref_idc: 1
[h264 @ 0x10b8635c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8635c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b86bf40] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b86bf40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8748c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8748c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b87d240] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b87d240] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b815f60] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b81e9c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b838640] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b840fc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b840fc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b849940] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b849940] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8522c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8522c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b85ac40] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b85ac40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8635c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8635c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b86bf40] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b86bf40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8748c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8748c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b87d240] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b87d240] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b815f60] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b81e9c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b838640] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b840fc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b840fc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b849940] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b849940] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8522c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8522c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b85ac40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b85ac40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8635c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8635c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 257 expected 1 got 2
[h264 @ 0x10b86bf40] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b86bf40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8748c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8748c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 257 expected 4 got 0
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 9 got 10
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82308060).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82295910).
[h264 @ 0x10b87d240] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b87d240] nal_unit_type: 7(SPS), nal_ref_idc: 1
[h264 @ 0x10b87d240] nal_unit_type: 8(PPS), nal_ref_idc: 1
[h264 @ 0x10b87d240] nal_unit_type: 5(IDR), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 1 got 2
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82314180).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82308060).
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b815f60] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 257 expected 2 got 4
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 9 got 10
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82317150).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82314180).
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b81e9c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 1 got 2
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82320210).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82317150).
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 9 got 10
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82323270).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82320210).
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 1 got 2
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82326330).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82323270).
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b838640] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 9 got 10
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82329300).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82326330).
[h264 @ 0x10b840fc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b840fc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 2 got 3
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82332360).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82329300).
[h264 @ 0x10b849940] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b849940] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 10 got 11
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82335420).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82332360).
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 8 got 9
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82338480).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82335420).
[h264 @ 0x10b8522c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b8522c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b85ac40] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b85ac40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 3 got 4
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82341450).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82338480).
[h264 @ 0x10b8635c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b8635c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 14 got 15
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82344510).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82341450).
[h264 @ 0x10b86bf40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b86bf40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 6 got 7
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82347570).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82344510).
[h264 @ 0x10b8748c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8748c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 3 got 4
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82350630).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82347570).
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 14 got 15
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82353600).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82350630).
[h264 @ 0x10b87d240] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b87d240] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b815f60] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 6 got 7
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82356660).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82353600).
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b81e9c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 1 got 2
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82359720).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82356660).
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 8 got 9
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82362690).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82359720).
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 0 got 1
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82365750).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82362690).
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b838640] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[mpegts @ 0x125f043d0] Continuity check failed for pid 256 expected 11 got 12
[mpegts @ 0x125f043d0] Packet corrupt (stream = 0, dts = 82368810).
[hls @ 0x125e0fa20] Packet corrupt (stream = 0, dts = 82365750).
[h264 @ 0x10b840fc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b840fc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[AVIOContext @ 0x135f3f450] Statistics: 89112 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x10bc1fc70] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] HLS request for url '330.ts', offset 0, playlist 0
[hls @ 0x125e0fa20] Opening '330.ts' for reading
[mpegts @ 0x125f043d0] Continuity check failed for pid 0 expected 1 got 0
[mpegts @ 0x125f043d0] Continuity check failed for pid 4097 expected 1 got 0
[h264 @ 0x10b849940] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b849940] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8522c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8522c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b85ac40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b85ac40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8635c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8635c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b86bf40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b86bf40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8748c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b8748c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b87d240] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b87d240] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b815f60] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b81e9c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b81e9c0] Frame num gap 2 0
[h264 @ 0x10b81e9c0] no picture ooo
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b827340] Frame num gap 5 3
[h264 @ 0x10b827340] no picture ooo
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b82fcc0] Frame num gap 9 7
[h264 @ 0x10b82fcc0] no picture ooo
[h264 @ 0x10b82fcc0] bytestream overread -5  43KB sq=    0B 
[h264 @ 0x10b82fcc0] error while decoding MB 8 37, bytestream -5
[h264 @ 0x10b82fcc0] concealing 110 DC, 110 AC, 110 MV errors in P frame
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b838640] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b838640] Frame num gap 11 9
[h264 @ 0x10b838640] no picture ooo
[h264 @ 0x10b840fc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b840fc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b840fc0] no picture ooo
[h264 @ 0x10b849940] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b849940] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b849940] no picture ooo
[h264 @ 0x10b8522c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8522c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8522c0] no picture ooo
[h264 @ 0x10b85ac40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b85ac40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b85ac40] no picture ooo
[h264 @ 0x10b8635c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b8635c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8635c0] no picture ooo
[h264 @ 0x10b86bf40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b86bf40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b86bf40] no picture ooo
[h264 @ 0x10b8748c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8748c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8748c0] no picture ooo
[h264 @ 0x10b87d240] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b87d240] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b87d240] no picture ooo
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b815f60] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b815f60] no picture ooo
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b81e9c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b81e9c0] no picture ooo
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b827340] no picture ooo
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b82fcc0] no picture ooo
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b838640] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b838640] no picture ooo
[h264 @ 0x10b840fc0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b840fc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b840fc0] no picture ooo
[h264 @ 0x10b849940] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b849940] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b849940] no picture ooo
[h264 @ 0x10b8522c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b8522c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b85ac40] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b85ac40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8635c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b8635c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b86bf40] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b86bf40] nal_unit_type: 7(SPS), nal_ref_idc: 1
[h264 @ 0x10b86bf40] nal_unit_type: 8(PPS), nal_ref_idc: 1
[h264 @ 0x10b86bf40] nal_unit_type: 5(IDR), nal_ref_idc: 1
[h264 @ 0x10b8748c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b8748c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b87d240] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b87d240] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b815f60] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b81e9c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b838640] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b840fc0] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b840fc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b849940] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b849940] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[AVIOContext @ 0x125e19490] Statistics: 58468 bytes read, 0 seeks
[hls @ 0x125e0fa20] HLS request for url '331.ts', offset 0, playlist 0
[hls @ 0x125e0fa20] Opening '331.ts' for reading
[mpegts @ 0x125f043d0] Continuity check failed for pid 0 expected 1 got 0
[mpegts @ 0x125f043d0] Continuity check failed for pid 4097 expected 1 got 0
[h264 @ 0x10b8522c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8522c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b85ac40] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b85ac40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8635c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8635c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b86bf40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b86bf40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8748c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8748c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b87d240] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b87d240] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b815f60] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b81e9c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b838640] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b840fc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b840fc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b849940] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b849940] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8522c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8522c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b85ac40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b85ac40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8635c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8635c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b86bf40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b86bf40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8748c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8748c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b87d240] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b87d240] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b815f60] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[AVIOContext @ 0x10b891d80] Statistics: 59972 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x136913740] Statistics: 507 bytes read, 0 seeks
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b81e9c0] nal_unit_type: 7(SPS), nal_ref_idc: 1
[h264 @ 0x10b81e9c0] nal_unit_type: 8(PPS), nal_ref_idc: 1
[h264 @ 0x10b81e9c0] nal_unit_type: 5(IDR), nal_ref_idc: 1
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b838640] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b840fc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b840fc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b849940] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b849940] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8522c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8522c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b85ac40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b85ac40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8635c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8635c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b86bf40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b86bf40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8748c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8748c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b87d240] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b87d240] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b815f60] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b81e9c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b838640] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b840fc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b840fc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b849940] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b849940] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8522c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8522c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b85ac40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b85ac40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8635c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8635c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b86bf40] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b86bf40] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b8748c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b8748c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b87d240] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b87d240] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b815f60] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b815f60] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b81e9c0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b81e9c0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b827340] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b827340] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[h264 @ 0x10b82fcc0] nal_unit_type: 9(AUD), nal_ref_idc: 0B 
[h264 @ 0x10b82fcc0] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x10be043c0] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x125f04840] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x10be043c0] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x127804080] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x127804080] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[h264 @ 0x10b838640] nal_unit_type: 9(AUD), nal_ref_idc: 0
[h264 @ 0x10b838640] nal_unit_type: 1(Coded slice of a non-IDR picture), nal_ref_idc: 1
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x122b048e0] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x10be04510] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x135f3f450] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x10b8919b0] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x10be04510] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x10be04510] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x125e19490] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x10be04510] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x122b048e0] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x10be04510] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x127804880] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x10b8919b0] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7') 0KB sq=    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x10be04510] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x10be04510] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x125f04840] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x127804880] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x125e19490] Statistics: 507 bytes read, 0 seeks
[hls @ 0x125e0fa20] Opening 'ffplay.m3u8' for reading    0B 
[hls @ 0x125e0fa20] Skip ('#EXT-X-VERSION:7')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:10.183Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:11.195Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:12.207Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:13.219Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:15.243Z')
[hls @ 0x125e0fa20] Skip ('#EXT-X-PROGRAM-DATE-TIME:2025-03-03T11:55:16.255Z')
[AVIOContext @ 0x10be04510] Statistics: 507 bytes read, 0 seeks
```
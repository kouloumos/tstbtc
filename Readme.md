# TRANSCRIBER TO BITCOIN TRANSCRIPT

This cli app transcribes audio and video for submission to the [bitcointranscripts](https://github.com/bitcointranscripts/bitcointranscripts) repo. 

**Available transcription models and services**

- (local) Whisper `--model xxx [default: tiny.en]`
- (remote) Deepgram (whisper-large) `--deepgram [default: False]`
  - summarization `--summarize`
  - diarization `--diarize`

**Features**:

- Transcription using [`openai-whisper`](https://github.com/openai/whisper) or [Deepgram](https://deepgram.com/)
- Collection of video's metadata when sourcing from YouTube.
- Open Pull Request on the [bitcointranscripts](https://github.com/bitcointranscripts/bitcointranscripts) repo for the resulting transcript.
- Save the resulting transcript to a markdown format supported by bitcointranscripts.
- Upload the resulting transcript to an AWS S3 Bucket
repo.
- Push the resulting transcript to [a Queuer backend](https://github.com/bitcointranscripts/transcription-review-backend), or save the payload in a json for later use.

## Prerequisites

- To use [deepgram](https://deepgram.com/) as a transcription service,
  you must have a valid `DEEPGRAM_API_KEY` in the `.env` file.

- To push the resulting transcript to a Queuer backend, you must have a 
  valid `QUEUE_ENDPOINT` in the `.env` file. If not, you can instead save
  the payload in a json file using the `--noqueue` flag.

- To enable us fork bitcointranscript repo and open a PR, we require you to
  login into your GitHub account. Kindly install `GITHUB CLI` using the
  instructions on their repo [here](https://github.com/cli/cli#installation).
  Following the prompt, please select the below options from the prompt to
  login:

    - what account do you want to log into? `Github.com`

    - what is your preferred protocol for Git operations? `SSH`

    - Upload your SSH public key to your GitHub account? `skip`

    - How would you like to authenticate GitHub CLI? `Login with a web browser`

    - copy the generated one-time pass-code and paste in the browser to
      authenticate if you have enabled 2FA

- To enable pushing the models to a S3 bucket,
    - [Install](https://aws.amazon.com/cli/) aws-cli to your system.
    - [Configure](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)
      aws-cli by first generating IAM credentials (if not already present) and
      using `aws configure` to set them.
    - To verify proper configuration, run `aws s3 ls` to show the list of S3
      buckets. Don't forget to set a valid `S3_BUCKET` in the `.env` file.

- To be able to convert the intermediary media files to mp3, install `FFmpeg`

    - for Mac Os users, run `brew install ffmpeg`

    - for other users, follow the instruction on
      their [site](https://ffmpeg.org/) to install

## Install/Uninstall

Navigate to the application directory and run the below commands:

`python3 -m venv venv` creates a virtual environment

`source venv/bin/activate` activates the virtual environment

`pip3 install . --use-pep517` to install the application

To check the version:
`tstbtc --version` view the application version

`tstbtc --help` view the application help

`pip3 uninstall tstbtc` to uninstall the application

## Usage

`tstbtc {source_file/url} {directory}` transcribe the given source

Suported sources:
  - YouTube videos
  - YouTube playlists
  - Local and remote audio files

Note:
- The `directory` is the bitcointranscripts directory that you want to associate the transcript with
- The https links need to be wrapped in quotes when running the command on zsh

To include optional metadata in your transcript, you can add the following
parameters:

- `-t` or `--title`: Add the title for the resulting transcript (required for audio files)
- `-d` or `--date`: Add the event date to transcript's metadata in format 'yyyy-mm-dd'
- can be used multiple times:
  - `-T` or `--tags`: Add a tag to transcript's metadata
  - `-s` or `--speakers`: Add a speaker to the transcript's metadata
  - `-c` or `--category`: Add a category to the transcript's metadata

To configure the transcription process, you can use the following flags:

- `-m` or `--model`: Select which whisper model to use for the transcription [default: tiny.en]
- `-D` or `--deepgram`: Use deepgram for transcription, instead of using the whisper model [default: False]
- `-M` or `--diarize`: Supply this flag if you have multiple speakers AKA want to diarize the content [only available with deepgram]
- `-S` or `--summarize`: Summarize the transcript [only available with deepgram]
- `-C` or `--chapters`: For YouTube videos, include the YouTube chapters and timestamps in the resulting transcript.
- `-p` or `--pr`: Open a PR on the bitcointranscripts repo
- `-u` or `--upload`: Upload processed model files to AWS S3
- `--markdown`: Save the resulting transcript to a markdown format supported by bitcointranscripts
- `--noqueue`: Do not push the resulting transcript to the Queuer, instead store the payload in a json file
- `--nocleanup`: Do not remove temp files on exit

### Examples

To transcribe [this podcast episode](https://www.youtube.com/watch?v=Nq6WxJ0PgJ4) from YouTube
from Stephan Livera's podcast and add the associated metadata, we would run either
of the below commands. The first uses short argument tags, while the second uses
long argument tags. The result is the same.

- `tstbtc Nq6WxJ0PgJ4 bitcointranscripts/stephan-livera-podcast -t 'OP_Vault - A New Way to HODL?' -d '2023-01-30' -T 'script' -T 'op_vault' -s 'James O’Beirne' -s 'Stephan Livera' -c ‘podcast’`
- `tstbtc Nq6WxJ0PgJ4 bitcointranscripts/stephan-livera-podcast --title 'OP_Vault - A New Way to HODL?' --date '2023-01-30' --tags 'script' --tags 'op_vault' --speakers 'James O’Beirne' --speakers 'Stephan Livera' --category ‘podcast’`

You can also transcribe a remote audio/mp3 link, such as the following from Stephan Livera's podcast: 
```shell
mp3_link="https://anchor.fm/s/7d083a4/podcast/play/64348045/https%3A%2F%2Fd3ctxlq1ktw2nl.cloudfront.net%2Fstaging%2F2023-1-1%2Ff7fafb12-9441-7d85-d557-e9e5d18ab788.mp3"
tstbtc $mp3_link bitcointranscripts/stephan-livera-podcast --title 'SLP455 Anant Tapadia - Single Sig or Multi Sig?' --date '2023-02-01' --tags 'multisig' --speakers 'Anant Tapadia' --speakers 'Stephan Livera' --category 'podcast'
```

## Testing

To run the unit tests

`pytest -v -m main -s`

To run the feature tests

`pytest -v -m feature -s`

To run the full test suite

`pytest -v -s`


## License

Transcriber to Bitcoin Transcript is released under the terms of the MIT
license. See [LICENSE](LICENSE) for more information or
see https://opensource.org/licenses/MIT.

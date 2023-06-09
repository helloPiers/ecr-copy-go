# ecr-copy

CLI utility that copies AWS ECR images between repositories in the same account and region, without using `docker` at all.

The copy is done using low-level ECR API calls which are described in the documentation as being used by the Amazon ECR proxy and not generally by customers.

> *Note*: For a more featured tool that uses docker APIs (but not a running docker daemon), checkout [`crane` from Google](https://github.com/google/go-containerregistry/blob/main/cmd/crane/doc/crane.md).

Note that for any image layers which need to be copied, the layer's content is downloaded (into memory) and re-uploaded, so a copy is likely to work faster and cheaper from an EC2 instance, Lambda, CloudShell, etc.

There's no published way to clean-up partial uploads in the event of subsequent error. I can't find any documentation about what happens to partially uploaded layers, or layers which are not part of any image.  I hope they are cleaned up in the background, but consider the possibility they are invisibly present forever (or until repository is deleted?) and charged for.

This utility assumes that things are how I found them to be in my particular use case. #YMMV

You'll need AWS credentials &mdash; provided in the usual ways &mdash; which allow read from the source repo and write to the destination.

## Installation

`go install hellopiers.io/ecr-copy@latest`

## Usage

`ecr-copy fromRepoName tagOrDigest toRepoName [new-tag]`

If the argument for _tagOrDigest_ is of the form `xxxx:hex` then it's treated as a digest, otherwise as a tag.

The output is on the verbose side.

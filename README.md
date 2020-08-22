# zsh Functions

A small collection of functions for zsh on macOS. (They may work on other operating systems and shells, but I can only vouch for macOS and zsh.) To use these functions, add the files to a directory on your `fpath` and then add `autoload <function name>` to your `.zshrc` file. (See [this page](https://scriptingosx.com/2019/07/moving-to-zsh-part-4-aliases-and-functions/) for an explanation of autoloading functions in zsh.)

## batchtc

`batchtc` transcodes all `.mkv` files in the current directory using [Other Video Transcoding](https://github.com/donmelton/other_video_transcoding).

The function can be called with no arguments or any arguments that `other-transcode` accepts. The function simply passes the arguments through to `batch.rb`, which in turn passes them through to `other-transcode`, so arguments must be passed exactly as `other-transcode` expects them.

Usage: `batchtc [OTHER-TRANSCODE ARGUMENTS]`

`batchtc` does three things:

1. Finds all `.mkv` files in the current directory.
2. Adds those file names to a file called `queue.txt`, also in the current directory.
3. Calls [batch.rb](https://github.com/donmelton/other_video_transcoding/wiki/Batching), passing any arguments that were passed to the function. For example, if you call `batchtc --mp4`, the function will call `batch.rb --mp4`.

The advantage of this function over calling `batch.rb` directly is that the function automatically builds the `queue.txt` file expected by `batch.rb`. (It would've been simpler to modify `batch.rb`, but I know shell scripting better than Ruby, and this function does the job.)

## openallin

`openallin` uses a specified application to open all files matching a given pattern.

The function can be called with up to four arguments as shown below. The first two arguments are required. The third is required only if you want to pass the fourth (see explanation below), and the fourth is always optional.

Usage: `openallin APPLICATION 'INCLUDE FILE PATTERN' [MAX DEPTH] ['EXCLUDE FILE PATTERN']`

The function accepts four arguments:

1. **APPLICATION** (required): The name of the application you want to use to open the files.
2. **INCLUDE FILE PATTERN** (required): A pattern that identifies the files you want to open. For example, `'*.jpg'` matches all files with the extension "jpg." This argument must be quoted so that the shell will interpret any wildcard characters as literal characters and not attempt to expand them.
3. **MAX DEPTH** (optional): The function uses the `find` command, and this argument is passed to `find` as the `-maxdepth` option. `find` will by default search for matching files in the current directory *and* every directory below the current directory; `maxdepth` limits the search. If you don't pass a third argument to `openallin`, the function uses "1" as the max depth and searches for files in the current directory only. If you want the function to search subdirectories, pass a number as the third argument. For example, "2" tells the function to look in the current directory and directories immediately under the current directory. If you don't know how deep the search needs to go, just take a guess. The function will search down to the specified max depth or until it hits the last directory, whichever comes first. Just keep in mind that the greater the number, the deeper the search will go, and the more files it will try to open. Don't pass a really large number as this argument.
4. **EXCLUDE FILE PATTERN** (optional): A pattern that identifies files you *don't* want to open. This is only needed if you want to open a subset of the files matched by the include file pattern. For example, if I just want to open JPG files that don't contain "thumbnail" in the file name (this was actually the scenario that prompted me to write the function), the command would be `openallin Pixelmator '*.jpg' 1 '*thumbnail*'`. Like **INCLUDE FILE PATTERN**, this argument must be quoted. Functions only accept positional parameters, and the function expects **EXCLUDE FILE PATTERN** to be the fourth argument. If you want to pass an exclude file pattern, you *must* also pass **MAX DEPTH**. (If you don't know what max depth to use, just pass '1'.)

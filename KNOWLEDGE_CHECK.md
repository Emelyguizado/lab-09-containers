# Lab 09: Knowledge check

**Answer each question below it, in your own words, in one to three sentences.** Replace the `...` below **Your answer:** with your text.

## 1. Credentials in the image?

Assume you want to share your image with others and make it publicly available on `ghcr.io`, so that anyone can pull it and run the analysis. A teammate suggests adding `COPY .aws /home/app/.aws` to the `Dockerfile`, so that nobody has to type `-v ~/.aws:/home/app/.aws:ro` any more. (This is a thought experiment: keep your own package private.)

**Your answer:** Give two reasons why this is a bad idea. Think of who could then read your credentials, and of what happens when your Learner Lab session ends.

I think its a bad idea to have credentials in the image because if the image is public, anyone who pulls it can open it and get your private AWS keys to access your account. Another reason is that the keys expire so the image would have keys that don't work inside, so it would stop working, and you'd have to rebuild it every time, while with -v you just update the credentials file.

## 2. Where did the Atlas password come from?

In Step 4, you ran the analysis once with `-v ~/.aws:/home/app/.aws:ro` only, without the three `-e` options. The container still wrote to your Atlas cluster.

*Hint:* Read `get_atlas_settings()` in `src/book_analysis/analyze.py`.

**Your answer:** Explain, step by step, where the container found the Atlas settings in that run.

To try and find the password, it first checks for the MONGODB_ATLAS_* environment variables but without -e, they weren't in the container. Then it falls back to AWS and reads the mongodb_credentials SSM parameter that I made in Lab 7 that holds the Atlas url, user, and password. It worked by looking in AWS because my credentials were mounted with -v ~/.aws.

## 3. Same tag, different image

Run `docker images book-analysis`, and note the image ID of `book-analysis:0.1.0`. Then change the `description` in the `LABEL` of your `Dockerfile`, build again with the same command, `docker build -t book-analysis:0.1.0 .`, and run `docker images book-analysis` again.

**Your answer:** Paste the image ID from before and after the rebuild. Why is it a problem for someone who uses your image that `0.1.0` now names a different image, and what should you have done instead?

My image id from before was f6b43a50bbbb and the one after is a564075cca06. The problem is that anyone who used 0.1.0 before is expecting the same image but now we changed it, so they could get different results without knowing why. Instead, we should raise the version in pyproject.toml and use a new tag like 0.1.1 to avoid this mix up.


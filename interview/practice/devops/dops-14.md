**14. Docker Volumes**
What are Docker volumes? What is the difference between volumes, bind mounts, and tmpfs mounts?

========== THIS SECTION IS THE ANSWER ==========

Volumes are Docker-managed storage that persists beyond container lifecycle — stored in Docker's own directory, portable across hosts. Bind mounts map a specific host path into the container — useful for development when you want live code reloading. Tmpfs mounts live only in memory, gone when container stops — good for secrets or temp data you don't want on disk.

In our project, we used bind mounts in development so code changes reflected immediately without rebuilding. In production, PostgreSQL data used Docker volumes so database survived container restarts. We used tmpfs for the container's `/tmp` directory to avoid writing sensitive intermediate files to disk.

The trade-off: bind mounts are convenient for development but create host dependency — your container only works if that host path exists. Volumes are portable and Docker manages them, but they're harder to inspect. Rule of thumb: volumes for production data, bind mounts for development, tmpfs for sensitive ephemeral data.

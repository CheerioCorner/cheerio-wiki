---
title: "Docker vs Podman: Why Podman Exists"
type: raw-transcript
created: 2026-09-05
video_id: Zvc5QkrWgAU
url: https://www.youtube.com/watch?v=Zvc5QkrWgAU
duration: 00:05:01
language: en
auto_generated: true
source_api: youtube_transcript_api
segments: 17
timestamp_format: "[MM:SS]"
---

# Docker vs Podman: Why Podman Exists

> Duration: 00:05:01 | Segments: 17 | Language: en | Source: API (auto-generated)

## Transcript

[00:00] Docker already lets you build and run containers. So why did Podman need to exist? The answer isn't that Podman runs containers while Docker doesn't. Both do. The interesting difference is what's happening behind the command you type.
[00:12] Let's start with the problem both tools are trying to solve. Imagine you have a Linux server and you want to run several containers on it. Something needs to create those containers, start and stop them, manage images, networking, and storage, and respond when you run commands. Docker and Podman both solve this problem. The interesting difference is how they choose to manage those containers. With Docker engine, the Docker CLI talks to a longunning background process called the Docker demon or Docker. When you run something
[00:41] like Docker run, the CLI sends the request to the demon and the demon handles the work of creating and managing the container. So you can think of the traditional Docker architecture as your command going to a central manager and that manager handling the containers. Podman takes a different approach. It is demonless. There isn't a single central Podman demon that has to stay running for you to manage your containers. Instead, the Podman CLI can manage containers without relying on a single long-running demon. That sounds
[01:08] like a small architectural difference, but it has important consequences, especially around security and how you operate containers. Let's say you're on a shared Linux server and several developers need to run containers. You probably don't want every developer to have unnecessary privileges on the host machine just to start a container. This is where Podman's rootless approach becomes interesting. Podman is designed to let users run containers as a non-root user which helps support a least privilege model. But this does not
[01:35] mean Docker can only run containers as root. Docker also supports rootless mode where both the Docker demon and containers can run without root privileges. So the important difference isn't Docker is root and Podman is not.
[01:47] The better way to think about it is that demonless and rootless container workflows are fundamental to Podman's design. While Docker traditionally uses a demon architecture and also provides rootless mode as an option, now you might be thinking, if I already know Docker, do I have to relearn containers to use Podman? Usually no. The basic command line experience is very familiar. You can build images, pull images, run containers, stop them, remove them, inspect them, and work with similar concepts. Podman even supports
[02:17] building container images and provides commands such as Podman run, Podman build, and Podman exec. There's another reason the transition can be relatively straightforward. Container images are designed around open standards. Podman works with OCI compatible container images. So the image format itself isn't tied to Docker. In practical terms, this means the same application image can often be used with either tool. Now let's look at a real world project.
[02:42] Imagine your application has a backend, a database, and a cache. You don't want to start and configure every container manually every time you work on the project. Docker Compose solves that by letting you describe the multicontainer application in a compose file and manage those services together. Podman can also work with compose-based workflows. But this is an area where Docker has a very mature ecosystem and developer experience. So if your team already has a large collection of Docker Compose
[03:09] projects, scripts, integrations, and tooling, that ecosystem can be an important reason to stay with Docker.
[03:16] Podman can also work with compose-based workflows through external compose providers such as podman compose. Podman also supports another concept called a pod. A pod lets you group multiple containers together so they can share certain resources including a network namespace. This becomes especially interesting if you've worked with Kubernetes because Kubernetes also uses the idea of pods to group containers.
[03:38] Podman isn't Kubernetes, but its pod model can make the concept easier to understand when you're working locally.
[03:45] Now let's come back to the main question. When should you use Docker and when should you consider Podman? If your team already uses Docker, your project depends heavily on Docker's ecosystem or you're learning containers for the first time. Docker is a very practical choice.
[03:59] You get a mature platform, familiar tooling and a huge ecosystem around it.
[04:04] But suppose you're primarily working with Linux and you specifically want a demonless architecture or a rootless first workflow. In that case, Podman becomes very interesting. It gives you a different way to manage containers while still keeping many of the concepts and workflows you're already familiar with.
[04:19] And that's the important thing to remember. Podman isn't trying to solve a completely different problem from Docker. Both are container engines designed to build and run containers.
[04:28] The biggest difference is the architecture behind them. Docker traditionally uses a central demon to manage containers. Podman is demonless and was designed around rootless container workflows. So don't think of this as Docker versus Podman. One doesn't simply replace the other. Think of it as two tools solving the same container problem with different design choices. Docker gives you a mature ecosystem and familiar developer workflows. Podman gives you a demonless approach with strong rootless support.
[04:54] Once you understand that architectural difference, the rest of the comparison becomes much easier to

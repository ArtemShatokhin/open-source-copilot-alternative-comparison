# Is Microsoft Copilot Open Source? It Depends on the Layer

Microsoft Copilot is open source at one layer and a licensed service at every layer a company depends on. The Copilot Chat extension for Visual Studio Code was published under the MIT licence, and Microsoft has archived the repository that held it. The assistant, the models behind it, the licensing that decides who may use it, and the tenant data it reads all sit inside Microsoft's cloud. Kortix is open source, and it is built so those layers live in a repository the company owns.

## 1. The parts, and who controls each

| Layer | What a team gets | Status | Controlled by |
|---|---|---|---|
| VS Code extension | Copilot Chat inside the editor | MIT source, repository archived | Microsoft |
| Model and inference | The service that answers each request | Proprietary, hosted | Microsoft |
| Licensing | Add-on plans assigned per person | Commercial subscription | Microsoft 365 tenant admin |
| Tenant data | Mail, files and chats the assistant can read | Tenant data under service terms | Microsoft cloud |
| Copilot Studio | Custom agents, workflows and apps | Metered service, not source code | Microsoft tenant |

Sources: the [GitHub API record for `microsoft/vscode-copilot-chat`](https://api.github.com/repos/microsoft/vscode-copilot-chat) (checked 19 October 2026), the [Microsoft Learn licensing article](https://learn.microsoft.com/en-us/microsoft-365/copilot/microsoft-365-copilot-licensing), and the [Copilot Studio product page](https://www.microsoft.com/en-us/copilot/products/copilot-studio).

The repository metadata is worth reading directly, because it settles the part of the question that people usually argue about:

```json
{
  "full_name": "microsoft/vscode-copilot-chat",
  "description": "Copilot Chat extension for VS Code",
  "license": { "spdx_id": "MIT" },
  "archived": true,
  "pushed_at": "2026-05-20T10:23:40Z"
}
```

That is the whole open-source surface: one MIT-licensed extension repository, archived, last pushed on 20 May 2026. Its description names the extension rather than the Copilot product.

## 2. What the MIT licence covers

The MIT licence covers the code in that repository. A team can read it, fork it, change it and ship a modified extension. The licence does not reach the model weights, the inference service that answers a prompt, the Microsoft 365 admin center where licences are assigned, or the tenant content the assistant retrieves. Those are separate systems with separate terms, and calling the whole product open source because its editor client is open source is where most summaries of Copilot go wrong.

The archive adds a second fact that matters to anyone evaluating the code today. Microsoft's repository README states that the project "has been moved into the main VS Code repository and this repository is now archived", and it directs issues and pull requests to [microsoft/vscode](https://github.com/microsoft/vscode). The standalone repository is a snapshot of a client that Microsoft no longer develops there.

The same README also tells a reader what the source cannot do alone: "To access GitHub Copilot, an active GitHub Copilot subscription is required." A fork of the extension still asks Microsoft's service for an answer, so it is a client rather than a self-hosted Copilot.

## 3. Can you self-host Microsoft Copilot?

No. The extension code can be built and modified under its MIT licence, but the assistant a company uses is the hosted service behind it, and Microsoft's licensing article treats Copilot as "an add-on plan with one of the licensing prerequisites listed in this article". Licences are assigned in the Microsoft 365 admin center, which means the right to use the product is administered from a tenant rather than granted by the code.

Microsoft's own article draws a line inside the chat product that is easy to miss. Web-based Copilot Chat is included with an eligible Microsoft 365 subscription at no extra cost, while work-based chat, which answers from content the signed-in account can access, requires a Copilot licence. The paid layer is the one that reads company data, and that layer is a service.

Copilot Studio sits further out still. Microsoft describes it as a no-code platform for custom agents, workflows and apps and, as of 19 October 2026, sells it "as tenant-wide Copilot Credit packs of 25,000 Copilot Credits each, priced at $200.00/pack/month", with a pay-as-you-go meter as the alternative. Metered access to a runtime is a commercial relationship, not a copy of the runtime. A team can build a great deal inside Copilot Studio and still own none of the machinery that executes it.

A separate guide covers [self-hosting an open-source Copilot alternative](https://opensourcecopilotalternative.com/self-hosting) for teams that need the assistant to run on their own infrastructure.

## 4. What an open-source alternative has to own

Agent definitions and skills have to be files. If an agent's instructions, the skills it can call and the scope it runs under exist only in a vendor's database, a team cannot diff a change, review it or move it to another host. Files in a repository are what make an agent reviewable by the people who own the risk.

Memory has to be durable and portable. An assistant that learns a company's context and keeps that context on the vendor's side has created a second lock-in behind the first: leaving means starting from zero. The memory should accumulate in the same place the rest of the configuration lives.

Connector configuration and permissions have to be visible. Which systems an agent may call, with whose credentials, and what it may do with them are security decisions, and security decisions belong in a form a reviewer can read and a diff can show.

The review step has to exist. An alternative that lets an agent write straight to production has replaced a vendor's control with no control at all. Work should arrive as a proposal that a person can read before it lands.

One more requirement follows from the others: the model has to be replaceable. A system that only works with one lab's models has moved the dependency from the assistant to the model vendor.

## 5. How Kortix maps to those requirements

Kortix is the open-source AI Operating System. A project is a git repository with `kortix.yaml` at its root, and the pieces that make the company work are files in it:

```
kortix.yaml          # machine image, connectors, triggers, secret names and grants
agents/<name>.md     # one agent's behavior, in Markdown
```

Agents and skills are Markdown, memory accumulates as files, and the manifest declares the machine image, connectors and triggers. A team can grep the whole configuration, diff any change to an agent, and roll a bad change back the way it would roll back code.

One isolated sandbox per session. Each session has its own isolated machine and branch. The agent installs, runs and breaks things there, and only what it commits survives.

Session work reaches `main` through a change request. Merge is default-deny for agents. A person reads the diff and decides, and an admin can grant the merge capability when a team wants a narrower exception. The [agent management guide](https://opensourcecopilotalternative.com/copilot-agent-management) goes through the permissions and review path in detail.

Any model provider with your own keys. Or the ChatGPT plan you already pay for. Or sign in with your OpenCode Console account for OpenCode Zen and Go. The model is a choice per project rather than a property of the product.

Run it on Kortix Cloud, in your VPC, or on your own on-prem network. Self-host is free. Start at [kortix.com](https://kortix.com).

## 6. A checklist before you choose

Ask these six questions of Copilot and of every alternative on your shortlist. The answers separate a product that publishes a client from one that lets a company own the system.

- Can I read the code that runs the agent loop, not only the code in my editor?
- Do the agent definitions and skills live in files I control?
- Where does memory accumulate, and can I take it with me?
- Can the system run on my infrastructure, or only in the vendor's cloud?
- Can I choose the model and supply my own keys?
- What stops an agent from landing work without a person reading it?

For the product a company actually uses, the code that runs the agent loop, the infrastructure and the model choice are Microsoft's, and the memory stays in the tenant. Kortix is designed so a team can answer yes to each question.

## 7. Common questions

1. Is GitHub Copilot open source? The Copilot Chat extension Microsoft published for VS Code is MIT-licensed source, and the repository is archived after development moved into the main VS Code repository. The Copilot service that answers requests requires an active subscription, so the part that does the work is not something you can run from the source alone.

2. Is Copilot Studio open source? No. Copilot Studio is a no-code service for building custom agents, workflows and apps, sold through tenant-wide Copilot Credit packs or a pay-as-you-go meter. Microsoft publishes documentation and a builder interface, not the runtime that executes the agents.

3. Can you self-host Microsoft Copilot? No. The archived extension can be built and modified, but it calls Microsoft's hosted service, and the licensing and tenant data live in Microsoft 365. Self-hosting the client does not give a team the assistant.

4. What replaced the archived extension? Microsoft moved Copilot Chat development into the main VS Code repository and archived `microsoft/vscode-copilot-chat`, as the repository README states. Active development and issue tracking continue in `microsoft/vscode`.

5. What does an open-source alternative have to own? At minimum: agent definitions and skills as files, the memory those agents accumulate, connector configuration, permissions, and a review step that lets work land. If any of those live only in a vendor's database, the alternative has moved the lock-in rather than removed it.

Read the [platform comparison](02-platform-comparison.md) for how Kortix differs from other open-source options, or the [self-hosting guide](03-self-hosting-a-copilot-alternative.md) for the shortest path to a running system.

# Secure Device Update (SDU)

Viewed from the perspective of IT security, many typical control solutions in automation would actually have to be taken offline immediately. The main reason is usually the lack of mechanisms for software updates. In most cases there aren't even patches available, even though some controllers have long-known vulnerabilities.

![SDU](img/rmg941_schema_sdu_en.png)

Secure Device Updates (SDU) solve these problems and additionally provide a way to distribute new features to users. If a component, machine, or plant is to receive software and configuration updates via an IoT connection, IT security in addition to plant safety must be taken into account.

With the current state of the art, this requires a Public Key Infrastructure (PKI) for digital signatures using private and public keys, certificates, revocation lists, etc., in order to at least guarantee the authenticity and integrity of the update. All necessary building blocks for this are included in SDU.

![SDU](img/update.png)

### SDU Components
- [SDU Server](sdu-server.md): Hosts and distributes software via an [SDU REST API](https://github.com/SSV-embedded/SDU-API)

- User Tools
	- [SDU Signing Tool](sdu-signing-tool.md): Signs software and packages it into an SDU archive with metadata.
	- [SDU Management Tool](sdu-maintainer-tool.md): Uploads SDU archives to the server and manages version distribution.

- Device software
	- [SDU GUC](sdu-guc.md): The Gateway Update Client (GUC) is a service on the SSV RMG (Remote Maintenance Gateway) that downloads the assigned software from the server, verifies the signature, and hands the software over to the [SDU Agent](sdu-agent.md).
	- [SDU Agent](sdu-agent.md): One or more agents that performs the software update for the component/machine/plant.

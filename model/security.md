# 3 Security

This section outlines generic security recommendations which should be considered when implementing the Dataspace protocol.

# 3.1 Token Security

Dataspaces use tokens for identity verification ("`ID tokens`") and optionally other means like authorization of certain message exchanges.
The identity standard used in a Dataspace is not defined within this document, but could be, for example, _OAuth2_ or Decentralized Identifiers using _did:web_.

It is the responsibility of adopters of this standard to consider relevant security threats and mitigate them wherever possible.

This subsection provides an incomplete list of attack vectors that should receive special attention.

# 3.1.1 Session Hijacking

Session Hijacking describes an attack where stolen or leaked session credentials, i.e. tokens, are used by possibly malicious actors that were not supposed to use them.

Protection against session hijacking can be accomplished by means like binding of tokens to public keys of their holder, used by transport protocol encryption. In the case of TLS-protected transports like HTTPS, this could be achieved through inclusion of certificate fingerprints, belonging to certificates used by the owner of the token, into the signed token.
Means like inclusion of the correct receiver(s) into token could also be an option, but may only partially mitigate the issue.

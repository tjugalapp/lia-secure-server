# Extern verifiering (Lynis)

Som ett komplement till CIS-genomgången kördes Lynis 3.1.6 mot servern (263 tester gav en hardening index på 72). Lynis är inte kopplat till CIS-benchmarken och ersätter inte CIS-dokumentationen. Verktyget användes som en oberoende andra kontroll.

Alla fynd granskades och föll i tre grupper:

- Falska positiver, till exempel att Lynis inte känner igen det nyare formatet på paketkällorna.
- Medvetna avvikelser, där Lynis rekommenderar åtgärder som går längre än CIS Level 1 eller där Docker/Kubernetes kräver andra inställningar.
- Förbättringar utanför projektets omfattning, alltså rekommendationer som hör till Level 2.
Samtliga kvarstående avvikelser från CIS Level 1 är medvetna och dokumenterade i respektive punkt.


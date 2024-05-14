# Pacman demo - gitops scenario

This repo contains a pacman deployment consisting of the following:

- pacman app
- mongo db & PVC (where high score data is stored)
- service
- route
- replicationsource (to backup the mongo PVC on a schedule using VolSync's restic mover)
- replicationdestination
- external-secret-restic-secret - this is an external secret and may need to be modified).  It's assuming the use
of the external secrets operator - when this secret is put down, the external secrets operator will create
the restic-secret used by the replicationsource and replicationdestination.  Currently setup to pull from a
secret in AWS using secretsmanager


This scenario assumes that the contents of this directory can be pushed down into a cluster and at the
end we should get pacman running with whatever high-score data was last saved in the latest restic backup.

Steps:
1. mongo PVC actually uses the volumepopulator from VolSync - that is, it points to the replicationdestination
and will not be provisioned until the replication destination connects to the datastore and pulls down the latest
backup from the restic repo.

If this is the 1st time deploy, there will be no saved data in the remote data store, no data will be pulled down 
and you get an empty pvc.

If there is saved data, the PVC will be populated with this saved data.  Since the VolSync volumepopulator is
used, the PVC will not become available until after this completes, so the pod using the PVC (mongo in this case)
will remain in pending until the PVC is populated.

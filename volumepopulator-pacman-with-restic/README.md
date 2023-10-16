Quick demo for VolumePopulator

- Using restic to show a backup/restore

- Prereqs:
  - S3 store for restic to copy backups into

1. Deploy pacman app from https://github.com/tesshuflower/demo/pacman

   Save a high score as the scores are what is persisted in the mongodb
   (the actual PVC used is called "mongo-storage")

1. edit restic-secret.yaml with credentials/location of the s3 bucket
   to backup to.

1. deploy restic-source.yaml and source/replicationsource.yaml
   to the pacman namespace to run a restic backup.

1. Now we can restore the PVC on another cluster or namespace.
   1. go to the cluster you want to restore to
   1. create the namespace to be used
   1. create the restic-secret.yaml
   1. create the restore-pvc-using-volumepopulator.yaml <- this one will use the volumepopulator
      that points to the replicationdestination in the next step
   1. create the replicationdestion.yaml

   Note that even though creating restore-pvc-using-volumepopulator.yaml happens
   before we even create the replicationdestination, the controller will
   not do anything until the replicationdestination shows up, then it will
   wait for the restore to complete (replication destination gets a latestImage
   that points to a snapshot) and then use that volume snapshot to populate
   the PVC.



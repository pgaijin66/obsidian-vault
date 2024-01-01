

- Git flow
	- Whatever is in master in in production
	- `develop` branch is the integration branch where new features are added and tested
	- `feature branche` are created off `develop` branch and merged back into it when completed
	- `release branch` are created off develop for final testing before merging into master for production
	- `hotfix branches` are created off master to address issues
- Feature based branching
	- `master` is production ready branch
	- `feature branch` is created off `master` branch for developing new feature
	- Once completed, it is merged back to `master`
- Release branching
	- `master` is the stable production branch
	- `release` branch are created off `master` branch for final testing and bug fixing before merging into `master` which is then deployed to production
- Trunk based development
	- `master`  is the development branch where all new feature are added and tested using feature flags
	- when feature is ready for release, the `feature flag` is removed and feature is release
	- `hotfix` branches are added directly to master branch

# Creating Maven Repo using GitHUB

A Test to make maven repo in git.

https://stackoverflow.com/questions/14013644/hosting-a-maven-repository-on-github/14013645#14013645

## Getting Started

This project can be cloned to make this test .

### Prerequisities

Maven should be installed.

```
mvn deploy
```

### Steps to make a repo with github.

# Step 1:

First, tell maven to deploy artifacts to a temporary staging location inside your target directory.

```
<distributionManagement>
    <repository>
        <id>internal.repo</id>
        <name>Temporary Staging Repository</name>
        <url>file://${project.build.directory}/mvn-repo</url>
    </repository>
</distributionManagement>

<plugins>
    <plugin>
        <artifactId>maven-deploy-plugin</artifactId>
        <version>2.8.1</version>
        <configuration>
            <altDeploymentRepository>internal.repo::default::file://${project.build.directory}/mvn-repo</altDeploymentRepository>
        </configuration>
    </plugin>
</plugins>
```

# Step 2:

Add your authentication information to ~/.m2/settings.xml so that the github site-maven-plugin can push to GitHub:

```
<!-- NOTE: MAKE SURE THAT settings.xml IS NOT WORLD READABLE! -->
<settings>
  <servers>
    <server>
      <id>github</id>
      <username>YOUR-USERNAME</username>
      <password>YOUR-PASSWORD</password>
    </server>
  </servers>
</settings>
```
OR

```
<settings>
  <servers>
    <server>
      <id>github</id>
      <username>GitHubLogin</username>
      <password>GitHubPassw0rd</password>
    </server>
  </servers>
</settings>
```

To get the OAUTH2TOKEN of the github project, please go to settings --> Applications --> Genreate new token

# Step 3:

Then tell the GitHub site-maven-plugin about the new server you just configured by adding the following to your pom

```
<properties>
    <!-- github server corresponds to entry in ~/.m2/settings.xml -->
    <github.global.server>github</github.global.server>
</properties>
```

# Step 4:

```
<plugin>
            <groupId>com.github.github</groupId>
            <artifactId>site-maven-plugin</artifactId>
            <version>0.11</version>
            <configuration>
                <message>Maven artifacts for ${project.version}</message>  <!-- git commit message -->
                <noJekyll>true</noJekyll>                                  <!-- disable webpage processing -->
                <outputDirectory>${project.build.directory}/mvn-repo</outputDirectory> <!-- matches distribution management repository url above -->
                <branch>refs/heads/mvn-repo</branch>                       <!-- remote branch name -->
                <includes><include>**/*</include></includes>
                <repositoryName>YOUR-REPOSITORY-NAME</repositoryName>      <!-- github repo name -->
                <repositoryOwner>YOUR-GITHUB-USERNAME</repositoryOwner>    <!-- github username  -->
                <server>github</server>
            </configuration>
            <executions>
              <!-- run site-maven-plugin's 'site' target as part of the build's normal 'deploy' phase -->
              <execution>
                <goals>
                  <goal>site</goal>
                </goals>
                <phase>deploy</phase>
              </execution>
            </executions>
        </plugin>
```

Congratulations!! You can now deploy your maven artifacts to a poor man's public repo simply by running 
```
mvn clean deploy
```

Visit github.com in your browser, select the mvn-repo branch, and verify that all your binaries are now there.

https://github.com/git-rbanerjee/maven-git-repo-test/tree/mvn-repo/com/coder/maven-git-repo-test

So , Now your public maven repo is ready.

# Step 5:

To use the repo ,Add github as repository source. And then use your project group/artifact/version as dependency.

```
<repositories>
    <repository>
        <id>YOUR-PROJECT-NAME</id>
        <url>https://raw.github.com/YOUR-USERNAME/YOUR-PROJECT-NAME/mvn-repo/</url>
        <snapshots>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
        </snapshots>
    </repository>
</repositories>
```

# That's All !!!

### To use this peoject as maven repo you can just use ,

```
    <repositories>
		<repository>
			<id>maven-git-repo-test</id>
			<url>https://raw.github.com/git-rbanerjee/maven-git-repo-test/mvn-repo/</url>
			<snapshots>
				<enabled>true</enabled>
				<updatePolicy>always</updatePolicy>
			</snapshots>
		</repository>
	</repositories>
```

And

```
    <dependency>
			<groupId>com.coder</groupId>
			<artifactId>maven-git-repo-test</artifactId>
			<version>0.0.1</version>
	</dependency>
```




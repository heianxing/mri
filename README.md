MRI (pronounced ehm-are-eye) helps to visualize flow of the code when using Axon Framework.

Installation
------------

	gradle jar

Usage
-----

1. Before running the code you need classpath for the analyzed project.

	For Maven run:

		mvn dependency:build-classpath | grep -v "^\[.*\].*" > this.classpath

	For Gradle add this to build.gradle:

		task showClasspath << {
    		it.println sourceSets.main.runtimeClasspath.collect { it.absolutePath }.join(':')
    	}

	and run

 		gradle -q showClasspath > this.classpath

2. To print the Axon flow:

		java -jar build/libs/org.mri-VERSION.jar -m (--method-name) METHOD_NAME -s (--source-folder) SOURCE_FOLDERS --classpath-file CLASSPATH_FILE
    
		--classpath-file CLASSPATH_FILE     : file containing the classpath for the analyzed project
		-c (--classpath) CLASSPATH          : classpath for the analyzed project
		-m (--method-name) METHOD_NAME      : method name (can be a regexp) to print axon flow for
		-s (--source-folder) SOURCE_FOLDERS : source folder(s) for the analyzed project
    
Example
-------

Execute following from this project root directory:

	$ gradle -q showClasspath > this.classpath
	$ java -jar build/libs//org.mri-*.jar -s src/main/java -m org.mri.ShowAxonFlow.main --classpath-file this.classpath

Output:

	org.axonframework.samples.trader.webui.init.BaseDBInit.createuser(java.lang.String, java.lang.String)
        -> org.axonframework.samples.trader.api.users.CreateUserCommand.CreateUserCommand(org.axonframework.samples.trader.api.users.UserId, java.lang.String, java.lang.String, java.lang.String)
        -- [handler] --
          -> org.axonframework.samples.trader.users.command.UserCommandHandler.handleCreateUser(org.axonframework.samples.trader.api.users.CreateUserCommand)
                -> org.axonframework.samples.trader.api.users.UserCreatedEvent.UserCreatedEvent(org.axonframework.samples.trader.api.users.UserId, java.lang.String, java.lang.String, java.lang.String)
                -- [listeners] --
                  -> org.axonframework.samples.trader.orders.command.PortfolioManagementUserListener.createNewPortfolioWhenUserIsCreated(org.axonframework.samples.trader.api.users.UserCreatedEvent)
                        -> org.axonframework.samples.trader.api.portfolio.CreatePortfolioCommand.CreatePortfolioCommand(org.axonframework.samples.trader.api.orders.trades.PortfolioId, org.axonframework.samples.trader.api.users.UserId)
                        -- [handler] --
                          -> org.axonframework.samples.trader.orders.command.PortfolioCommandHandler.handleCreatePortfolio(org.axonframework.samples.trader.api.portfolio.CreatePortfolioCommand)

                  -> org.axonframework.samples.trader.query.users.UserListener.handleUserCreated(org.axonframework.samples.trader.api.users.UserCreatedEvent)

                  -> org.axonframework.samples.trader.users.command.User.onUserCreated(org.axonframework.samples.trader.api.users.UserCreatedEvent)
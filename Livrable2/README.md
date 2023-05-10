# Livrable 2
Pour le deuxième livrable, nous allons livrer les parties suivantes de notre application:

    1. Rafinement des APIs et documentation swagger
    2. Page connexion et création de compte pour l'application mobile avec Flutter
    3. Début du client web en Svelte
    4. Tous les micro-services sur Docker
    5. Utilisation de consul et adminer

## Rafinement des APIs et documentation swagger
Nous avons améliorer les APIS développés au livrable 1. Par exemple, le micro-service *account* contient une nouvelle entité *Moderator*. Un utilisateur peut donc maintenant être un modérateur d'une certaine communauté. Nous avons aussi ajouté la documentation *Swagger* à tous nos APIs.

### Micro-service: communauté
```java
// Entity: community
package com.example.ms_community.model;

import java.sql.Date;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

@Table(name = "community")
@Entity
@Getter 
@Setter 
public class Community {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Integer id;

    @Column(name = "Community name")
    private String communityName;

    @Column(name = "Description")
    private String communityDescription;

    @Column(name = "Community logo")
    private String communityLogo;

    @Column(name = "Community header image")
    private String communityHeaderImage;

    @Column(name = "Created on")
    private Date communityCreatedOnDate;

    @Column(name = "Ammount of members")
    private Integer communityAmmountOfMembers;

    @Column(name = "Ammount of posts")
    private Integer communityAmmountOfPosts;

    @Column(name = "Community creator id")
    private Integer communityCreatorId;

    public Community() {

    }

    public Community(Community newCommunity) {
        this.communityName = newCommunity.getCommunityName();
        this.communityDescription = newCommunity.getCommunityDescription();
        this.communityCreatedOnDate = newCommunity.getCommunityCreatedOnDate();
        this.communityCreatorId = newCommunity.getCommunityCreatorId();
    }
}
```

```java
// Community Controller
package com.example.ms_community.controller;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import io.swagger.v3.oas.annotations.responses.ApiResponses;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.example.ms_community.repository.CommunityRepository;
import com.example.ms_community.model.Community;;

@CrossOrigin
@RestController
@RequestMapping("/communities")
public class CommunityController {
    @Autowired
    private CommunityRepository communityRepository;

    @Operation(summary = "Adds a community do the database")
    @ApiResponses(value = {
            @ApiResponse(responseCode = "201", description = "Community created", content = {
                    @Content(mediaType = "application/json", schema = @Schema(implementation = Community.class)) }),
            @ApiResponse(responseCode = "400", description = "Invalid data", content = @Content),
            @ApiResponse(responseCode = "500", description = "Internal server error", content = @Content)
    })
    @PostMapping("/add")
    public ResponseEntity<Community> addNewCommunity(@RequestBody Community newCommunity) {
        try {
            Community communityToAdd = communityRepository.save(new Community(newCommunity));
            return new ResponseEntity<>(communityToAdd, HttpStatus.CREATED);
        } catch (Exception e) {
            return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }

    @Operation(summary = "Gets all communities")
    @ApiResponses(value = {
            @ApiResponse(responseCode = "200", description = "Found communities", content = {
                    @Content(mediaType = "application/json", schema = @Schema(implementation = Community.class)) }),
            @ApiResponse(responseCode = "404", description = "The communities were not found", content = @Content),
            @ApiResponse(responseCode = "500", description = "Internal server error", content = @Content)
    })
    @GetMapping("/view/all")
    public ResponseEntity<List<Community>> getAllCommunities() {
        try {
            List<Community> CommunityList = new ArrayList<Community>();

            communityRepository.findAll().forEach(CommunityList::add);

            if (CommunityList.isEmpty()) {
                return new ResponseEntity<>(HttpStatus.NO_CONTENT);
            }

            return new ResponseEntity<>(CommunityList, HttpStatus.OK);
        } catch (Exception e) {
            return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }

    @Operation(summary = "Get a community by its id")
    @ApiResponses(value = {
            @ApiResponse(responseCode = "200", description = "Found the community", content = {
                    @Content(mediaType = "application/json", schema = @Schema(implementation = Community.class)) }),
            @ApiResponse(responseCode = "400", description = "Invalid id supplied", content = @Content),
            @ApiResponse(responseCode = "404", description = "The community was not found", content = @Content),
            @ApiResponse(responseCode = "500", description = "Internal server error", content = @Content)
    })
    @GetMapping("/view/{id}")
    public ResponseEntity<Community> getCommunityById(@PathVariable("id") long id) {
        Optional<Community> CommunityData = communityRepository.findById(id);

        if (CommunityData.isPresent()) {
            return new ResponseEntity<>(CommunityData.get(), HttpStatus.OK);
        } else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }

    @Operation(summary = "Edits a community by its id")
    @ApiResponses(value = {
            @ApiResponse(responseCode = "200", description = "Community updated", content = {
                    @Content(mediaType = "application/json", schema = @Schema(implementation = Community.class)) }),
            @ApiResponse(responseCode = "400", description = "Invalid id supplied", content = @Content),
            @ApiResponse(responseCode = "404", description = "The community was not found", content = @Content),
            @ApiResponse(responseCode = "500", description = "Internal server error", content = @Content)
    })
    @PutMapping("/edit/{id}")
    public ResponseEntity<Community> updateCommunity(@PathVariable("id") long id,
            @RequestBody Community updateCommunity) {
        Optional<Community> communityData = communityRepository.findById(id);

        if (communityData.isPresent()) {
            Community modifiedCommunity = communityData.get();
            modifiedCommunity = new Community(updateCommunity);
            return new ResponseEntity<>(communityRepository.save(modifiedCommunity), HttpStatus.OK);
        } else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }

    @Operation(summary = "Deletes a community by its id")
    @ApiResponses(value = {
            @ApiResponse(responseCode = "200", description = "Community deleted", content = {
                    @Content(mediaType = "application/json", schema = @Schema(implementation = Community.class)) }),
            @ApiResponse(responseCode = "400", description = "Invalid id supplied", content = @Content),
            @ApiResponse(responseCode = "404", description = "The community was not found", content = @Content),
            @ApiResponse(responseCode = "500", description = "Internal server error", content = @Content)
    })
    @DeleteMapping("/delete/{id}")
    public ResponseEntity<Community> deleteCommunity(@PathVariable("id") long id) {
        Optional<Community> communityData = communityRepository.findById(id);
        if (communityData.isPresent()) {
            try {
                Community deletedCommunity = communityData.get();
                communityRepository.deleteById(id);
                return new ResponseEntity<>(deletedCommunity, HttpStatus.OK);
            } catch (Exception e) {
                return new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
            }
        } else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }
}
```

```java
// Community Repository
package com.example.ms_community;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MsCommunityApplication {

	public static void main(String[] args) {
		SpringApplication.run(MsCommunityApplication.class, args);
	}

}
```
### Micro-service: account
```java
// Entity: account
package com.example.ms_account.model;

import java.time.LocalDate;

import jakarta.persistence.*;
import lombok.Getter;
import lombok.Setter;

@Entity
@Getter
@Setter
@Table(name = "accounts")
public class Account {
    
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;

    @Column(name = "userLastName")
    private String userLastName;

    @Column(name = "userFirstName")
    private String userFirstName;

    @Column(name = "userEmail")
    private String userEmail;

    @Column(name = "userPseudo")
    private String userPseudo;

    @Column(name = "userPassword")
    private String userPassword;

    @Column(name = "userBirthday")
    private LocalDate userBirthday;

    public Account() {

    }

    public Account(String userLastName, String userFirstName, String userEmail, String userPseudo,
            String userPassword, LocalDate userBirthday) {
        this.userLastName = userLastName;
        this.userFirstName = userFirstName;
        this.userEmail = userEmail;
        this.userPseudo = userPseudo;
        this.userPassword = userPassword;
        this.userBirthday = userBirthday;
    }


}
```

```java
// Entity: moderator
package com.example.ms_account.model;

import jakarta.persistence.*;
import lombok.Getter;
import lombok.Setter;

@Entity
@Getter
@Setter
@Table(name = "moderators")
public class Moderator {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;

    @Column(name = "moderatorUserId")
    private long moderatorUserId;

    @Column(name = "moderatorComId")
    private long moderatorComId;

    public Moderator() {
    }

    public Moderator(long moderatorUserId, long moderatorComId) {
        this.moderatorUserId = moderatorUserId;
        this.moderatorComId = moderatorComId;
    }

}
```

```java
//Account Controller
package com.example.ms_account.controller;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

import com.example.ms_account.model.Account;
import com.example.ms_account.repository.AccountRepository;

import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import io.swagger.v3.oas.annotations.responses.ApiResponses;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@CrossOrigin
@RestController
@RequestMapping("/accounts")
public class AccountController {
    
    @Autowired
    AccountRepository accountRepository;

    @Operation(summary = "Get all accounts")
    @ApiResponses(value = { 
        @ApiResponse (responseCode = "200", description = "Found the accounts", 
        content = {@Content (mediaType = "application/json",
        schema = @Schema (implementation = Account.class))}),
        @ApiResponse(responseCode = "404", description = "Could not find the accounts", 
            content = @Content),
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
        content = @Content) })

    @GetMapping("/view/all")
    public ResponseEntity<List<Account>> getAllAccounts() {
        try {
            List<Account> accountsList = new ArrayList<Account>();

            accountRepository.findAll().forEach(accountsList::add);

            if(accountsList.isEmpty()) {
                return new ResponseEntity<>(HttpStatus.NO_CONTENT);
            }

            return new ResponseEntity<>(accountsList, HttpStatus.OK);
        } catch (Exception e) {
            return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }

    @Operation(summary = "Get an account by Id")
    @ApiResponses(value = { 
        @ApiResponse (responseCode = "200", description = "Account found", 
        content = {@Content (mediaType = "application/json",
        schema = @Schema (implementation = Account.class))}),
        @ApiResponse(responseCode = "404", description = "Account not found", 
            content = @Content),
        @ApiResponse(responseCode = "400", description = "Invalid id", 
        content = @Content),
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
        content = @Content) })

    @GetMapping("/view/{id}")
    public ResponseEntity<Account> getAccountById(@PathVariable("id") long id) {
        Optional<Account> accountData = accountRepository.findById(id);

        if(accountData.isPresent()) {
            return new ResponseEntity<>(accountData.get(), HttpStatus.OK);
        }
        else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }

    @Operation(summary = "Get an account by pseudo and password")
    @ApiResponses(value = { 
        @ApiResponse (responseCode = "200", description = "Account found", 
        content = {@Content (mediaType = "application/json",
        schema = @Schema (implementation = Account.class))}),
        @ApiResponse(responseCode = "404", description = "Account not found", 
            content = @Content),
        @ApiResponse(responseCode = "400", description = "Invalid data", 
        content = @Content),
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
        content = @Content) })

    @GetMapping("/view/by_pseudo/{pseudo}/{password}")
    public ResponseEntity<Account> getAccountByPseudoAndPassword(@PathVariable("pseudo") String pseudo, @PathVariable("password") String password) {
        Optional<Account> accountData = accountRepository.findByUserPseudoAndUserPassword(pseudo, password);

        if(accountData.isPresent()) {
            return new ResponseEntity<>(accountData.get(), HttpStatus.OK);
        }
        else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }

    @Operation(summary = "Add an account")
    @ApiResponses(value = { 
        @ApiResponse (responseCode = "201", description = "Account added", 
        content = {@Content (mediaType = "application/json",
        schema = @Schema (implementation = Account.class))}),
        @ApiResponse(responseCode = "400", description = "Invalid data", 
            content = @Content),
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
        content = @Content) })

    @PostMapping("/add")
    public ResponseEntity<Account> createAccount(@RequestBody Account account) {
        try {
            Account accountToAdd = accountRepository.save(new Account(account.getUserLastName(), account.getUserFirstName(), account.getUserEmail(), 
                                                                    account.getUserPseudo(), account.getUserPassword(), account.getUserBirthday()));
            return new ResponseEntity<>(accountToAdd, HttpStatus.CREATED);
        }
        catch (Exception e) {
            return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }

    @Operation(summary = "Edit an account")
    @ApiResponses(value = { 
        @ApiResponse (responseCode = "200", description = "Account edited", 
        content = {@Content (mediaType = "application/json",
        schema = @Schema (implementation = Account.class))}),
        @ApiResponse(responseCode = "400", description = "Invalid data", 
            content = @Content),
        @ApiResponse(responseCode = "404", description = "Account not found", 
        content = @Content),
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
        content = @Content) })

    @PutMapping("/edit/{id}")
    public ResponseEntity<Account> updateAccount(@PathVariable("id") long id, @RequestBody Account account) {
        Optional<Account> accountData = accountRepository.findById(id);

        if(accountData.isPresent()) {
            Account modifiedAccount = accountData.get();
            modifiedAccount.setUserLastName(account.getUserLastName());
            modifiedAccount.setUserFirstName(account.getUserFirstName());
            modifiedAccount.setUserEmail(account.getUserEmail());
            modifiedAccount.setUserPseudo(account.getUserPseudo());
            modifiedAccount.setUserPassword(account.getUserPassword());
            modifiedAccount.setUserBirthday(account.getUserBirthday());

            return new ResponseEntity<>(accountRepository.save(modifiedAccount), HttpStatus.OK);
        }
        else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }

    @Operation(summary = "Delete an account")
    @ApiResponses(value = { 
        @ApiResponse (responseCode = "200", description = "Account deleted", 
        content = {@Content (mediaType = "application/json",
        schema = @Schema (implementation = Account.class))}),
        @ApiResponse(responseCode = "405", description = "Invalid data", 
            content = @Content),
        @ApiResponse(responseCode = "404", description = "Account not found", 
        content = @Content),
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
        content = @Content) })

    @DeleteMapping("/delete/{id}")
        public ResponseEntity<HttpStatus> deleteAccount(@PathVariable("id") long id) {
            Optional<Account> accountData = accountRepository.findById(id);
            if(accountData.isPresent()) {
                try {
                    accountRepository.deleteById(id);
                    return new ResponseEntity<>(HttpStatus.OK);
                }
                catch (Exception e) {
                    return new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
                }
            }
            else {
                return new ResponseEntity<>(HttpStatus.NOT_FOUND);
            }
        }
}
```

```java
// Moderator controller
package com.example.ms_account.controller;
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

import com.example.ms_account.model.Moderator;
import com.example.ms_account.repository.ModeratorRepository;

import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import io.swagger.v3.oas.annotations.responses.ApiResponses;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@CrossOrigin
@RestController
@RequestMapping("/moderators")
public class ModeratorController {

    @Autowired
    ModeratorRepository moderatorRepository;

    @Operation(summary = "Get all moderators")
    @ApiResponses(value = { 
        @ApiResponse (responseCode = "200", description = "Found the moderators", 
        content = {@Content (mediaType = "application/json",
        schema = @Schema (implementation = Moderator.class))}),
        @ApiResponse(responseCode = "404", description = "Could not find the moderators", 
            content = @Content),
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
        content = @Content) })

    @GetMapping("view/all")
    public ResponseEntity<List<Moderator>> getAllModerators() {
        try {
            List<Moderator> moderatorsList = new ArrayList<Moderator>();
            moderatorRepository.findAll().forEach(moderatorsList::add);

            if(moderatorsList.isEmpty()) {
                return new ResponseEntity<>(HttpStatus.NO_CONTENT);
            }

            return new ResponseEntity<>(moderatorsList, HttpStatus.OK);
        }
        catch(Exception e) {
            return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }


    @Operation(summary = "Get moderator by Id")
    @ApiResponses(value = { 
        @ApiResponse (responseCode = "200", description = "Moderator found", 
        content = {@Content (mediaType = "application/json",
        schema = @Schema (implementation = Moderator.class))}),
        @ApiResponse(responseCode = "404", description = "Moderator not found", 
            content = @Content),
        @ApiResponse(responseCode = "400", description = "Invalid id", 
        content = @Content),
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
        content = @Content) })

    @GetMapping("view/{id}")
    public ResponseEntity<Moderator> getModeratorById(@PathVariable("id") long id) {
        Optional<Moderator> moderatorData = moderatorRepository.findById(id);

        if(moderatorData.isPresent()) {
            return new ResponseEntity<>(moderatorData.get(), HttpStatus.OK);
        }
        else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }


    @Operation(summary = "Get list of moderators by UserId")
    @ApiResponses(value = { 
        @ApiResponse (responseCode = "200", description = "List of moderators found", 
        content = {@Content (mediaType = "application/json",
        schema = @Schema (implementation = Moderator.class))}),
        @ApiResponse(responseCode = "404", description = "Moderators not found", 
            content = @Content),
        @ApiResponse(responseCode = "400", description = "Invalid id", 
        content = @Content),
        @ApiResponse(responseCode = "204", description = "No moderator for this user ID", 
        content = @Content), 
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
        content = @Content) })

    @GetMapping("view/by_user/{id}")
    public ResponseEntity<List<Moderator>> getModeratorsByUserId(@PathVariable ("id") long id) {
        try {
            List<Moderator> moderatorData = new ArrayList<Moderator>();
            moderatorRepository.findBymoderatorUserId(id).forEach(moderatorData::add);

            if (moderatorData.isEmpty()) {
                return new ResponseEntity<>(HttpStatus.NO_CONTENT);
            }
            return new ResponseEntity<>(moderatorData, HttpStatus.OK);
        } catch (Exception e) {
            return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }

    @GetMapping("view/by_community/{id}")
    public ResponseEntity<List<Moderator>> getModeratorsByCommunityId(@PathVariable ("id") long id) {
        try {
            List<Moderator> moderatorData = new ArrayList<Moderator>();
            moderatorRepository.findBymoderatorComId(id).forEach(moderatorData::add);

            if (moderatorData.isEmpty()) {
                return new ResponseEntity<>(HttpStatus.NO_CONTENT);
            }
            return new ResponseEntity<>(moderatorData, HttpStatus.OK);
        } catch (Exception e) {
            return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }


    @Operation(summary = "Add a moderator")
    @ApiResponses(value = { 
        @ApiResponse (responseCode = "201", description = "Moderator added", 
        content = {@Content (mediaType = "application/json",
        schema = @Schema (implementation = Moderator.class))}),
        @ApiResponse(responseCode = "400", description = "Invalid data", 
            content = @Content),
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
        content = @Content) })

    @PostMapping("/add")
    public ResponseEntity<Moderator> createModerator(@RequestBody Moderator moderator) {
        try {
            Moderator moderatorToAdd = moderatorRepository.save(new Moderator(moderator.getModeratorUserId(), moderator.getModeratorComId()));
            return new ResponseEntity<>(moderatorToAdd, HttpStatus.CREATED);
        } catch (Exception e) {
            return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }


    @Operation(summary = "Edit moderator")
    @ApiResponses(value = { 
        @ApiResponse (responseCode = "200", description = "Moderator edited", 
        content = {@Content (mediaType = "application/json",
        schema = @Schema (implementation = Moderator.class))}),
        @ApiResponse(responseCode = "400", description = "Invalid data", 
            content = @Content),
        @ApiResponse(responseCode = "404", description = "Moderator not found", 
        content = @Content),
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
        content = @Content) })

    @PutMapping("/edit/{id}")
    public ResponseEntity<Moderator> updateModerator(@PathVariable("id") long id, @RequestBody Moderator moderator) {
        Optional<Moderator> moderatorData = moderatorRepository.findById(id);

        if(moderatorData.isPresent()) {
            Moderator modifiedModerator = moderatorData.get();
            modifiedModerator.setModeratorUserId(moderator.getModeratorUserId());
            modifiedModerator.setModeratorComId(moderator.getModeratorComId());

            return new ResponseEntity<>(moderatorRepository.save(modifiedModerator), HttpStatus.OK);
        }
        else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }


    @Operation(summary = "Delete a moderator")
    @ApiResponses(value = { 
        @ApiResponse (responseCode = "200", description = "Moderator deleted", 
        content = {@Content (mediaType = "application/json",
        schema = @Schema (implementation = Moderator.class))}),
        @ApiResponse(responseCode = "405", description = "Invalid data", 
            content = @Content),
        @ApiResponse(responseCode = "404", description = "Moderator not found", 
        content = @Content),
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
        content = @Content) })

    @DeleteMapping("/delete/{id}")
    public ResponseEntity<HttpStatus> deleteModerator(@PathVariable("id") long id) {
        Optional<Moderator> moderatorData = moderatorRepository.findById(id);
        if(moderatorData.isPresent()) {
            try {
                moderatorRepository.deleteById(id);
                return new ResponseEntity<>(HttpStatus.OK);
            }
            catch (Exception e) {
                return new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
            }
        }
        else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }  
}
```

```java
// Account Repository
package com.example.ms_account.repository;

import java.util.Optional;

import org.springframework.data.jpa.repository.JpaRepository;
import com.example.ms_account.model.Account;

public interface AccountRepository extends JpaRepository<Account, Long> {
    Optional<Account> findByUserPseudoAndUserPassword(String userPseudo, String userPassword);
}
```

```java
// Moderator Repository
package com.example.ms_account.repository;

import java.util.List;

import org.springframework.data.jpa.repository.JpaRepository;

import com.example.ms_account.model.Moderator;

public interface ModeratorRepository extends JpaRepository<Moderator, Long>  {
    List<Moderator> findBymoderatorUserId(long moderatorUserId);
    List<Moderator> findBymoderatorComId(long moderatorComId);
}
```

### Micro-service: post
```java
// Entity: post
package com.example.ms_post.model;

import java.time.LocalDate;
import jakarta.persistence.*;
import lombok.Data;
import lombok.Getter;
import lombok.Setter;

@Data
@Entity
@Getter
@Setter
@Table(name="post")
public class Post {
    
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private long id;

    @Column(name = "postTitle")
    private String postTitle;

    @Column(name="postContent")
    private String postContent;

    @Column(name="postSource")
    private String postSource;
    
    @Column(name="postDate")
    private LocalDate postDate;

    @Column(name="postUpvote")
    private Integer postUpvote;

    @Column(name="postDownvote")
    private Integer postDownvote;

    @Column(name="postIdUser")
    private long postIdUser;

    @Column(name="postIdCom")
    private Integer postIdCom;

    public Post() {
        
    }

    public Post(String postTitle, String postContent, String postSource, LocalDate postDate, long postIdUser, Integer postIdCom) {
        this.postTitle = postTitle;
        this.postContent = postContent;
        this.postSource = postSource;
        this.postDate = postDate;
        this.postUpvote = 0;
        this.postDownvote = 0;
        this.postIdUser = postIdUser;
        this.postIdCom = postIdCom;
    }

    @Override
    public String toString() {
        return "Post [id=" + id + ", postTitle=" + postTitle + ", postContent=" + postContent + ", postSource="
                + postSource + ", postDate=" + postDate + ", postUpvote=" + postUpvote + ", postDownvote="
                + postDownvote + ", postIdUser=" + postIdUser + ", postIdCom=" + postIdCom + "]";
    }
 
}
```

```java
// Post controller
package com.example.ms_post.controller;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.example.ms_post.model.Post;
import com.example.ms_post.repository.PostRepository;

import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import io.swagger.v3.oas.annotations.responses.ApiResponses;

@CrossOrigin(origins = "http://localost:8081")
@RestController
@RequestMapping("/posts")
public class PostController {

    @Autowired
    PostRepository postRepository;

    @Operation(summary = "Get all posts")
    @ApiResponses(value = {
        @ApiResponse(responseCode = "200", description = "Found the posts",
        content = {  @Content(mediaType = "application/json",
        schema = @Schema(implementation = Post.class)) }), 
        @ApiResponse(responseCode = "404", description = "Posts not found", 
            content = @Content),
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
            content = @Content) }) 

    @GetMapping("/view/all")
    public ResponseEntity<List<Post>> getAllPosts() {
        try {
            List<Post> posts = new ArrayList<Post>();

            postRepository.findAll().forEach(posts::add);
            
            if(posts.isEmpty()) {
                return new ResponseEntity<>(HttpStatus.NO_CONTENT);
                     }
            return new ResponseEntity<>(posts, HttpStatus.OK);

        } catch (Exception e) {
            return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }


    @Operation(summary = "Get a post by Id")
    @ApiResponses(value = { 
        @ApiResponse (responseCode = "200", description = "Post found", 
        content = {@Content (mediaType = "application/json",
        schema = @Schema (implementation = Post.class))}),
        @ApiResponse(responseCode = "404", description = "Post not found", 
            content = @Content),
        @ApiResponse(responseCode = "400", description = "Invalid id", 
        content = @Content),
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
        content = @Content) })

    @GetMapping("/view/{id}")
    public ResponseEntity<Post> getAccountById(@PathVariable("id") long id) {
        Optional<Post> postData = postRepository.findById(id);

        if(postData.isPresent()) {
            return new ResponseEntity<>(postData.get(), HttpStatus.OK);
        }
        else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }

    @Operation(summary = "Get a post by its user ID")
    @ApiResponses(value = {
        @ApiResponse(responseCode = "200", description = "Found the post",
        content = {  @Content(mediaType = "application/json",
        schema = @Schema(implementation = Post.class)) }), 
        @ApiResponse(responseCode = "204", description = "No post for this user ID", 
        content = @Content), 
        @ApiResponse(responseCode = "400", description = "Invalid user ID supplied", 
            content = @Content), 
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
            content = @Content) }) 
                 

    @GetMapping("/view/by_user/{id}")
    public ResponseEntity<List<Post>> getPostbyUserId(@PathVariable ("id") long id) {
        try {
            List<Post> postData = new ArrayList<Post>();
            postRepository.findBypostIdUser(id).forEach(postData::add);
            
            if (postData.isEmpty()) {
                return new ResponseEntity<>(HttpStatus.NO_CONTENT);
            }
            return new ResponseEntity<>(postData, HttpStatus.OK);
        } catch (Exception e) {
            return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }

    @Operation(summary = "Add a post")
    @ApiResponses(value = {
        @ApiResponse(responseCode = "201", description = "Post created",
        content = {  @Content(mediaType = "application/json",
        schema = @Schema(implementation = Post.class)) }), 
        @ApiResponse(responseCode = "400", description = "Invalid data", 
            content = @Content), 
        @ApiResponse(responseCode = "500", description = "Internal Server Error", 
            content = @Content) }) 

    @PostMapping("/add")
    public ResponseEntity<Post> createPost(@RequestBody Post post) {
        try {
            Post _post = postRepository.save(new Post(post.getPostTitle(), post.getPostContent(), post.getPostSource(), post.getPostDate(), post.getPostIdUser(), post.getPostIdCom()));
            return new ResponseEntity<>(_post,HttpStatus.CREATED);
        } catch (Exception e) {
            return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }

    @Operation(summary = "Edit a post by its ID")
    @ApiResponses(value = { 
        @ApiResponse(responseCode = "200", description = "Post updated", 
            content = { @Content(mediaType = "application/json", 
            schema = @Schema(implementation = Post.class)) }),
        @ApiResponse(responseCode = "400", description = "Invalid id supplied", 
            content = @Content), 
        @ApiResponse(responseCode = "404", description = "The post was not found", 
            content = @Content),
        @ApiResponse(responseCode = "500", description = "Internal server error", 
            content = @Content)
        })

    @PutMapping("/edit/{id}")
    public ResponseEntity<Post> updatePost(@PathVariable("id") long id, @RequestBody Post post) {
        Optional<Post> postData = postRepository.findById(id);

        if (postData.isPresent()) {
            Post modifiedPost = postData.get();
            modifiedPost.setPostTitle(post.getPostTitle());   
            modifiedPost.setPostContent(post.getPostContent());
            modifiedPost.setPostDate(post.getPostDate());
            modifiedPost.setPostSource(post.getPostSource());
            modifiedPost.setPostUpvote(post.getPostUpvote());
            modifiedPost.setPostDownvote(post.getPostDownvote());
            modifiedPost.setPostIdUser(post.getPostIdUser());
            modifiedPost.setPostIdCom(post.getPostIdCom());
            return new ResponseEntity<>(postRepository.save(modifiedPost), HttpStatus.OK);    
        }
        else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }

    @Operation(summary = "Delete a post by its ID")
    @ApiResponses(value = { 
        @ApiResponse(responseCode = "200", description = "Post deleted", 
            content = { @Content(mediaType = "application/json", 
            schema = @Schema(implementation = Post.class)) }),
        @ApiResponse(responseCode = "400", description = "Invalid id supplied", 
            content = @Content), 
        @ApiResponse(responseCode = "404", description = "The post was not found", 
            content = @Content),
        @ApiResponse(responseCode = "500", description = "Internal server error", 
            content = @Content)
        })

    @DeleteMapping("/delete/{id}")
    public ResponseEntity<HttpStatus> deletePost(@PathVariable("id") long id) {
        Optional<Post> postData = postRepository.findById(id);
        if (postData.isPresent()) {
            try {
                postRepository.deleteById(id);
                return new ResponseEntity<>(HttpStatus.OK);
              } catch (Exception e) {
                return new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
              }
        }
        else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
     
    }

}
```

```java
// Post repository
package com.example.ms_post.repository;

import java.util.List;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import com.example.ms_post.model.Post;

@Repository
public interface PostRepository extends JpaRepository<Post, Long> {
    List<Post> findBypostIdUser(long postIdUser);
   
}
```

### Screenshot: documentation Swagger

### Community
![Doc swagger - community](./images/swagger-community.png)

### Account
![Doc swagger - account](./images/swagger-account.png)

### Post
![Doc swagger - post](./images/swagger-post.png)


## Page connexion et création de compte pour l'application mobile avec Flutter
Nous avons créé deux premières pages pour l'interface graphique: la page de connexion et la page de création de compte. Les deux pages sont connectés au micro-service *ms_account* et permettent à un utilisateur de s'inscrire et de se connecter. 

### Account
Pour pouvoir communiquer avec l'API, il faut créer une entité *Account*
```dart
class Account {
  final int userId;
  final String userLastName;
  final String userFirstName;
  final String userEmail;
  final String userPseudo;
  final String userPassword;
  final DateTime userBirthday;

  const Account({
    required this.userId,
    required this.userLastName,
    required this.userFirstName,
    required this.userEmail,
    required this.userPseudo,
    required this.userPassword,
    required this.userBirthday,
  });

  // permet de transformer une entité en format json pour la communication avec l'API
  factory Account.fromJson(Map<String, dynamic> json) {
    return Account(
        userId: json['userId'],
        userLastName: json['userLastName'],
        userFirstName: json['userFirstName'],
        userEmail: json['userEmail'],
        userPseudo: json['userPseudo'],
        userPassword: json['userPassword'],
        userBirthday: json['userBirthday']);
  }
}
```

### Fichier configuration
Nous avons réuni les configurations communes entre plusieurs pages dans un fichier: *config.dart* afin de faciliter la lecture du code
```dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';

class Configuration {
  static const appDarkBackgroundColor = Color.fromARGB(255, 31, 30, 30);
  static const orangeColor = Color(0xffff6633);

  static TextStyle textForApp(Color color, double fontSize) {
    return GoogleFonts.roboto(
        textStyle: TextStyle(
            fontWeight: FontWeight.bold,
            color: color,
            letterSpacing: .5,
            fontSize: fontSize));
  }

  static Container inputField(
      BuildContext context, TextEditingController controller, String hintText) {
    return Container(
      alignment: Alignment.center,
      child: SizedBox(
          height: 50,
          width: MediaQuery.of(context).size.width * 0.9,
          child: TextFormField(
            controller: controller,
            cursorColor: Colors.black,
            style: const TextStyle(color: Colors.white),
            decoration: InputDecoration(
              hintStyle: const TextStyle(color: Colors.white60),
              hintText: hintText,
              filled: true,
              fillColor: Colors.grey.shade700,
              border: OutlineInputBorder(
                  borderSide: BorderSide.none,
                  borderRadius: BorderRadius.circular(20)),
            ),
            validator: (String? value) {
              if (value == null || value.isEmpty) {
                return 'Please enter some text';
              }
              return null;
            },
          )),
    );
  }

  static Container passwordInput(
      BuildContext context, TextEditingController controller, String hintText) {
    return Container(
      alignment: Alignment.center,
      child: SizedBox(
        height: 50,
        width: MediaQuery.of(context).size.width * 0.9,
        child: TextFormField(
          obscureText: true,
          enableSuggestions: false,
          autocorrect: false,
          controller: controller,
          cursorColor: Colors.black,
          style: const TextStyle(color: Colors.white),
          decoration: InputDecoration(
            hintStyle: const TextStyle(color: Colors.white60),
            hintText: hintText,
            filled: true,
            fillColor: Colors.grey.shade700,
            border: OutlineInputBorder(
                borderSide: BorderSide.none,
                borderRadius: BorderRadius.circular(20)),
          ),
          validator: (String? value) {
            if (value == null || value.isEmpty) {
              return 'Please enter some text';
            }
            return null;
          },
        ),
      ),
    );
  }

  static ButtonStyle formButtonStyle(BuildContext context) {
    return ElevatedButton.styleFrom(
      backgroundColor: Colors.grey.shade800,
      minimumSize: Size(MediaQuery.of(context).size.width * 0.95, 45),
      padding: const EdgeInsets.symmetric(horizontal: 16.0),
      shape: const RoundedRectangleBorder(
        borderRadius: BorderRadius.all(Radius.circular(25.0)),
      ),
    );
  }
}
```
### Fichier utilities
Nous avons réuni des fonctions utiles à l'application dans un fichier *utilities.dart*

```dart
import 'dart:convert';
import 'package:flutter/material.dart' hide Key;
import 'package:http/http.dart' as http;
import 'package:interface_mobile/account.dart';

// Sources:
// How to use alert dialogs
// https://www.javatpoint.com/flutter-alert-dialogs

// Http request with flutter
// https://docs.flutter.dev/cookbook/networking/send-data
// https://docs.flutter.dev/cookbook/networking/fetch-data

// Hash text
// https://medium.flutterdevs.com/explore-encrypt-decrypt-data-in-flutter-576425347439

showAlertDialog(BuildContext context, String text) {
  // Create button
  Widget okButton = ElevatedButton(
    child: const Text("OK"),
    onPressed: () {
      Navigator.of(context).pop();
    },
  );

  // Create AlertDialog
  AlertDialog alert;
  alert = AlertDialog(
    title: const Text('Ohé!'),
    content: Text(text),
    actions: [
      okButton,
    ],
  );

  // show the dialog
  showDialog(
    context: context,
    builder: (BuildContext context) {
      return alert;
    },
  );
}

// connecter un utilisateur
Future<Account> userConnect(
    BuildContext context, String pseudo, String password) async {
  final response = await http.get(Uri.parse(
      'http://10.0.2.2:8082/accounts/view/by_pseudo/$pseudo/$password'));

  if (response.statusCode == 200) {
    showAlertDialog(context, "Connexion réussie");
    return Account.fromJson(jsonDecode(response.body));
  } else {
    showAlertDialog(context, "Erreur de connexion");
    throw Exception('Failed to connect');
  }
}

// créer un nouvel utilisateur
Future<Account> createUser(
    BuildContext context,
    String lastName,
    String firstName,
    String email,
    String pseudo,
    String password,
    String birthday) async {
  final response = await http.post(
    Uri.parse('http://10.0.2.2:8082/accounts/add'),
    headers: <String, String>{
      'Content-Type': 'application/json; charset=UTF-8',
    },
    body: jsonEncode(<String, String>{
      'userLastName': lastName,
      'userFirstName': firstName,
      'userEmail': email,
      'userPseudo': pseudo,
      'userPassword': password,
      'userBirthday': birthday
    }),
  );

  if (response.statusCode == 201) {
    showAlertDialog(context, "Compte créé avec succès");
    return Account.fromJson(jsonDecode(response.body));
  } else {
    throw Exception('Failed to create user.');
  }
}
```

### Page connexion

```dart
import 'package:flutter/gestures.dart';
import 'package:flutter/material.dart';
import 'package:interface_mobile/config.dart';
import 'package:interface_mobile/creationcompte.dart';
import 'package:interface_mobile/utilities.dart';

class ConnexionPage extends StatelessWidget {
  const ConnexionPage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Configuration.appDarkBackgroundColor,
      appBar: AppBar(
        leading: IconButton(
            icon: const Icon(
              Icons.arrow_back,
            ),
            onPressed: () {}),
        title: const Center(
          child: Icon(
            Icons.anchor,
            color: Configuration.orangeColor,
          ),
        ),
        actions: <Widget>[
          Padding(
            padding: const EdgeInsets.fromLTRB(0, 15, 20, 0),
            child: Text.rich(TextSpan(children: [
              TextSpan(
                  style: Configuration.textForApp(Colors.white, 18),
                  text: "Sign up",
                  recognizer: TapGestureRecognizer()
                    ..onTap = () async {
                      Navigator.push(
                        context,
                        MaterialPageRoute(
                            builder: (context) => const CreationCompte()),
                      );
                    }),
            ])),
          )
        ],
        backgroundColor: Configuration.appDarkBackgroundColor,
      ),
      body: const SingleChildScrollView(child: ConnexionForm()),
    );
  }
}

class ConnexionForm extends StatefulWidget {
  const ConnexionForm({super.key});

  @override
  State<ConnexionForm> createState() => ConnexionFormState();
}

class ConnexionFormState extends State<ConnexionForm> {
  final GlobalKey<FormState> _formKey = GlobalKey<FormState>();
  final TextEditingController pseudoController = TextEditingController();
  final TextEditingController passwordController = TextEditingController();

  @override
  void dispose() {
    pseudoController.dispose();
    passwordController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: <Widget>[
          Padding(
              padding: const EdgeInsets.all(15),
              child: Center(
                child: Text('Log in to Hublot',
                    style: Configuration.textForApp(Colors.white, 25)),
              )),
          Padding(
              padding: const EdgeInsets.all(8),
              child: Configuration.inputField(
                  context, pseudoController, "Username")),
          Padding(
            padding: const EdgeInsets.all(8),
            child: Configuration.passwordInput(
                context, passwordController, "Password"),
          ),
          Padding(
            padding: const EdgeInsets.all(8),
            child: Text(
              'Forgot password',
              style: Configuration.textForApp(Configuration.orangeColor, 16),
            ),
          ),
          Padding(
            padding: const EdgeInsets.symmetric(vertical: 50.0),
            child: Center(
              child: ElevatedButton(
                style: Configuration.formButtonStyle(context),
                onPressed: () {
                  // si le formulaire est validé
                  if (_formKey.currentState!.validate()) {
                    var pseudo = pseudoController.text;
                    var password = passwordController.text;
                    userConnect(context, pseudo, password);
                  }
                },
                child: const Text('Continue'),
              ),
            ),
          ),
        ],
      ),
    );
  }
}
```

### Page création de compte
La création de compte est divisé sur deux pages

```dart
// Première page
import 'package:flutter/gestures.dart';
import 'package:flutter/material.dart';
import 'package:interface_mobile/config.dart';
import 'package:interface_mobile/creationcompteNext.dart';

import 'connexion.dart';

class CreationCompte extends StatelessWidget {
  const CreationCompte({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Configuration.appDarkBackgroundColor,
      appBar: AppBar(
        leading: IconButton(
            icon: const Icon(
              Icons.arrow_back,
            ),
            onPressed: () {}),
        title: const Center(
          child: Icon(
            Icons.anchor,
            color: Configuration.orangeColor,
          ),
        ),
        actions: <Widget>[
          Padding(
            padding: const EdgeInsets.fromLTRB(0, 15, 20, 0),
            child: Text.rich(TextSpan(children: [
              TextSpan(
                  style: Configuration.textForApp(Colors.white, 18),
                  text: "Log in",
                  recognizer: TapGestureRecognizer()
                    ..onTap = () async {
                      Navigator.push(
                        context,
                        MaterialPageRoute(
                            builder: (context) => const ConnexionPage()),
                      );
                    }),
            ])),
          )
        ],
        backgroundColor: Configuration.appDarkBackgroundColor,
      ),
      body: const SingleChildScrollView(child: CreationCompteForm()),
    );
  }
}

class CreationCompteForm extends StatefulWidget {
  const CreationCompteForm({super.key});

  @override
  State<CreationCompteForm> createState() => CreationCompteFormState();
}

class CreationCompteFormState extends State<CreationCompteForm> {
  final GlobalKey<FormState> _formKey = GlobalKey<FormState>();
  final TextEditingController pseudoController = TextEditingController();
  final TextEditingController passwordController = TextEditingController();

  @override
  void dispose() {
    pseudoController.dispose();
    passwordController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: <Widget>[
          Padding(
            padding: const EdgeInsets.all(8),
            child:
                Configuration.inputField(context, pseudoController, "Username"),
          ),
          Padding(
            padding: const EdgeInsets.all(8),
            child: Configuration.passwordInput(
                context, passwordController, "Password"),
          ),
          Padding(
              padding: const EdgeInsets.symmetric(vertical: 50.0),
              child: Center(
                child: ElevatedButton(
                  style: Configuration.formButtonStyle(context),
                  onPressed: () {
                    // Validate will return true if the form is valid, or false if
                    // the form is invalid.
                    if (_formKey.currentState!.validate()) {
                      Navigator.push(
                        context,
                        MaterialPageRoute(
                            builder: (context) => CreationCompteNext(
                                username: pseudoController.text,
                                password: passwordController.text)),
                      );
                    }
                  },
                  child: const Text('Next'),
                ),
              )),
        ],
      ),
    );
  }
}

```

```dart
// Deuxième page
import 'package:flutter/gestures.dart';
import 'package:flutter/material.dart';
import 'package:interface_mobile/config.dart';
import 'package:interface_mobile/utilities.dart';
import 'package:intl/intl.dart';

import 'connexion.dart';

// Sources
// Datepicker in Flutter
// https://www.fluttercampus.com/guide/39/how-to-show-date-picker-on-textfield-tap-and-get-formatted-date/

class CreationCompteNext extends StatelessWidget {
  const CreationCompteNext(
      {super.key, required this.username, required this.password});

  final String username;
  final String password;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Configuration.appDarkBackgroundColor,
      appBar: AppBar(
        leading: IconButton(
            icon: const Icon(
              Icons.arrow_back,
            ),
            onPressed: () {}),
        title: const Center(
          child: Icon(
            Icons.anchor,
            color: Configuration.orangeColor,
          ),
        ),
        actions: <Widget>[
          Padding(
            padding: const EdgeInsets.fromLTRB(0, 15, 20, 0),
            child: Text.rich(TextSpan(children: [
              TextSpan(
                  style: Configuration.textForApp(Colors.white, 18),
                  text: "Log in",
                  recognizer: TapGestureRecognizer()
                    ..onTap = () async {
                      Navigator.push(
                        context,
                        MaterialPageRoute(
                            builder: (context) => const ConnexionPage()),
                      );
                    }),
            ])),
          )
        ],
        backgroundColor: Configuration.appDarkBackgroundColor,
      ),
      body: SingleChildScrollView(
          child: CreationCompteNextForm(
        username: username,
        password: password,
      )),
    );
  }
}

class CreationCompteNextForm extends StatefulWidget {
  const CreationCompteNextForm(
      {super.key, required this.username, required this.password});

  final String username;
  final String password;

  @override
  State<CreationCompteNextForm> createState() => CreationCompteNextFormState();
}

class CreationCompteNextFormState extends State<CreationCompteNextForm> {
  final GlobalKey<FormState> _formKey = GlobalKey<FormState>();
  final TextEditingController lastNameController = TextEditingController();
  final TextEditingController firstNameController = TextEditingController();
  final TextEditingController emailController = TextEditingController();
  final TextEditingController pseudoController = TextEditingController();
  final TextEditingController passwordController = TextEditingController();
  final TextEditingController birthdayController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: <Widget>[
          Padding(
            padding: const EdgeInsets.all(8),
            child: Configuration.inputField(
                context, lastNameController, "Last Name"),
          ),
          Padding(
            padding: const EdgeInsets.all(8),
            child: Configuration.inputField(
                context, firstNameController, "First Name"),
          ),
          Padding(
            padding: const EdgeInsets.all(8),
            child: Configuration.inputField(context, emailController, "Email"),
          ),
          Padding(
            padding: const EdgeInsets.all(8),
            child: Container(
              alignment: Alignment.center,
              child: SizedBox(
                height: 50,
                width: MediaQuery.of(context).size.width * 0.9,
                child: TextFormField(
                  style: const TextStyle(color: Colors.white),
                  controller: birthdayController,
                  decoration: InputDecoration(
                      icon: Icon(
                        Icons.calendar_today,
                        color: Colors.grey.shade700,
                      ),
                      filled: true,
                      fillColor: Colors.grey.shade700,
                      border: OutlineInputBorder(
                          borderSide: BorderSide.none,
                          borderRadius: BorderRadius.circular(20)),
                      hintText: "Birthday",
                      hintStyle: const TextStyle(color: Colors.grey)),
                  readOnly:
                      true, //set it true, so that user will not able to edit text
                  onTap: () async {
                    DateTime? pickedDate = await showDatePicker(
                        context: context,
                        initialDate: DateTime.now(),
                        firstDate: DateTime(
                            1900), //DateTime.now() - not to allow to choose before today.
                        lastDate: DateTime.now());

                    if (pickedDate != null) {
                      String formattedDate =
                          DateFormat('yyyy-MM-dd').format(pickedDate);
                      setState(() {
                        birthdayController.text =
                            formattedDate; //set output date to TextField value.
                      });
                    } else {
                      showAlertDialog(
                          context, "Veuillez sélectionner une date.");
                    }
                  },
                ),
              ),
            ),
          ),
          Padding(
              padding: const EdgeInsets.symmetric(vertical: 50.0),
              child: Center(
                child: ElevatedButton(
                  style: Configuration.formButtonStyle(context),
                  onPressed: () {
                    // Validate will return true if the form is valid, or false if
                    // the form is invalid.
                    if (_formKey.currentState!.validate()) {
                      var lastName = lastNameController.text;
                      var firstName = firstNameController.text;
                      var email = emailController.text;
                      var pseudo = widget.username;
                      var password = widget.password;
                      var birthday = birthdayController.text;
                      createUser(context, lastName, firstName, email, pseudo,
                          password, birthday);
                    }
                  },
                  child: const Text('Create Account'),
                ),
              )),
        ],
      ),
    );
  }
}
```

### Sceenshots: résultats
Nous avons essayé de reproduire le plus possible l'application mobile Reddit.

#### Connexion
![Page connexion](./images/mobile-connection.png)

Si l'utilisateur existe dans la base de données, le résultat est le suivant:
![Page connexion - succès](./images/mobile-connection-success.png)

Si l'utilisateur n'existe pas dans la base données ou entre le mauvais mot de passe, le résultat est le suivant:
![Page connexion - erreur](./images/mobile-connection-error.png)

#### Création de compte
Voici la première page lors de la création d'un compte utilisateur:
![Page connexion - 1](./images/newuser-page1.png)

Voici la deuxième page lors de la création d'un compte utilisateur:
![Page connexion - 2](./images/newuser-page2.png)

Quand un utilisateur est ajouté avec succès, voici ce qui arrive:
![Page connexion - succès](./images/newuser-success.png)

Quand nous allons vérifier dans la base de données, l'utilisateur est bien ajouté:
![Nouvel utilisateur base de données](./images/newuser-bd.png)


## Début du client web en Svelte

## Tous les micro-services sur Docker

## Utilisation de consul et adminer
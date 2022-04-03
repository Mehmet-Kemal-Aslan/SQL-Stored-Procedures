# SQL-Stored-Procedures
Eski bilgisayarım bozulunca SQL dosyalarını kabettim. Bunlar Saklı yordam kodları.

I lost the SQL files when my old computer broke down. These are stored procedure codes.

USE [Blog]
GO
/** Object:  StoredProcedure [dbo].[sp_Article]    Script Date: 16.1.2022 07:02:47 **/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO

ALTER PROCEDURE [dbo].[sp_Article] 
 @ISLEM INT,
 @TITLE VARCHAR(300),
 @BODY VARCHAR(MAX),
 @USER_ID INT,
 @TAG_ID VARCHAR(MAX),
 @CATEGORY_ID INT,
 @YORUM_A_ID INT,
 @YORUM_USERNAME VARCHAR(300),
 @YORUM_USERMAIL VARCHAR(300),
 @YORUM_USERCOMMENT VARCHAR(300)
AS
BEGIN
	SET NOCOUNT ON;

	IF @ISLEM = 1
	BEGIN
		SELECT 
				A.Id AS Article_Id,
				A.Title,
				A.Body,
				A.Date,
				P.Id as Profile_Id,
				P.Name as Profile_Name,
				C.Id as Category_Id,
				C.Name as Category_Name,
				COMMENTS = (SELECT CM.Id, CM.Username, CM.Usercomment, CM.Usermail, CM.Date  FROM Comment CM WHERE Article_Id = A.Id FOR JSON AUTO),
				TAGS = (SELECT * FROM Tag T WHERE T.Id IN (SELECT ATG.Tag_Id FROM ArticleTags ATG WHERE ATG.Article_Id = A.Id) FOR JSON AUTO)
			FROM Article A
			JOIN Profile P ON A.Profile_Id = P.Id
			JOIN Category C ON A.Category_Id = C.Id
	END
	ELSE IF @ISLEM = 2	
	BEGIN
		DECLARE @ARTICLE_ID INT = 0;

		INSERT INTO Article(Category_Id, Profile_Id, Title, Body, Date)
		VALUES(@CATEGORY_ID, @USER_ID, @TITLE, @BODY, getdate());

		SET @ARTICLE_ID = SCOPE_IDENTITY();

		INSERT INTO ArticleTags (Article_Id, Tag_Id)
		SELECT @ARTICLE_ID AS Article_Id, VALUE AS Tag_Id FROM OPENJSON(@TAG_ID) 
	

	END
	ELSE IF @ISLEM = 3
	BEGIN
		INSERT INTO Comment(Article_Id, Username, Usermail, Usercomment, Date)
		VALUES(@YORUM_A_ID, @YORUM_USERNAME, @YORUM_USERMAIL, @YORUM_USERCOMMENT, GETDATE());


	END
	ELSE IF @ISLEM = 4
	BEGIN
			SELECT 
				A.Id AS Article_Id,
				A.Title,
				A.Body,
				A.Date,
				P.Id as Profile_Id,
				P.Name as Profile_Name,
				C.Id as Category_Id,
				C.Name as Category_Name,
				COMMENTS = (SELECT CM.Id, CM.Username, CM.Usercomment, CM.Usermail, CM.Date  FROM Comment CM WHERE Article_Id = A.Id FOR JSON AUTO),
				TAGS = (SELECT * FROM Tag T WHERE T.Id IN (SELECT ATG.Tag_Id FROM ArticleTags ATG WHERE ATG.Article_Id = A.Id) FOR JSON AUTO)
			FROM Article A
			JOIN Profile P ON A.Profile_Id = P.Id
			JOIN Category C ON A.Category_Id = C.Id
			WHERE A.Date>='2021-09-01'
			and A.Date<'2021.10.01'
	END
END
